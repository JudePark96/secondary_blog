---
layout: post
title: Game Matching System Implementation
date: 2018-02-24
Excerpt: "Implementation of Game Matching System"
tag: [Celery, Python]
---

### 1. Introduction

이번에 롤에서의 게임 매칭과 같은 기능을 구현해야하는 일이 생겼고 처음엔 쉽게 생각하였으나 개발하면 할 수록 공부한 내용이 많은 것 같아서 글로 남겨놓는다. 

### 2. What I thought is ...

일반적으로 우리가 생각하는 게임 매칭의 간단한 시나리오라 하면 다음과 같다.

1. 대기열에 게임 플레이를 요청한 사용자의 정보를 넣는다.
2. n:n 으로 매치해준다.

이를 깊숙히 파헤쳐보면 다양한 시나리오 및 우려할 점이 나온다.

1. A 와 B 가 이미 매칭이 되었는데, 만약 C 와 B 가 매칭이 되었다면?
   1. C 는 매칭에 실패한 것이기 때문에 다시 대기열로 복원해야한다.
2. A 와 B 가 이미 매칭이 되었는데, 도중에 B 가 매칭 취소한다면?
   1. A 가 매칭을 취소한 것이 아닌 이상, A 를 다시 대기열로 복원해야한다.
3. 한 번의 너무 많은 양의 사용자가 들어온다면?
   1. Celery 와 같은 Distributed Task Queue 를 사용하자.
4. 비동기적으로 작성할 때, 발생할 수 있는 문제를 어떻게 해결해야할까?
   1. Async / Await 를 확실히 이해하고 작성하자.
   2. try .. except 를 사용한다.

이 밖에도 다양한 것이 있겠지만 일단 이 정도가 대표적일 것 같다.

### 3. Celery Is All You Need

`What I thought is …` 에서 생각한 것을 종합해본 결과, [Celery](http://www.celeryproject.org/) 를 사용하는 것이 가장 합리적인 생각을 하였다. 일정한 Frequency 마다 대기열에 쌓여있는 사용자들을 매칭하는 Task 를 작성하면 될 것이라고 생각하였다.

Socket.io 의 에서 background 에서 정의한 Frequency 마다 Task 를 호출하는 방식으로 구현하기로 정했다.

결과적으로, Task 의 호출 주기가 5초라고 가정했을 때, 게임 매치 요청 이벤트를 통해 Redis 에 사용자 정보가 쌓이며 5초가 되었을 때, Celery 에서 한 번에 매칭을 하고 결과를 Socekt.io 로 넘긴다. 이 결과를 바탕으로 생성된 Room Id, Players Information, Players Socket Id 등을 넘겨 Room 을 생성하고 Emit 을 통해, 매칭이 완료되었음을 알린다.

### 4. Implementation

```Python
import redis
from celery import Celery
from itertools import groupby

# Redis Configuration
r = redis.StrictRedis(host=localhost, port=port, db=0, decode_responses=True)

# Celery Configuration
app = Celery('matching_tasks', backend=redis_localhost, broker=redis_localhost)
```

위의 코드는 Redis 와 Celery 에 접근하기 위한 Configuration 이라고 보면 될 것 같다.

```Python
@app.task(name='tasks.matching_task.matching_task.game_matching_task')
def game_matching_task():
    response = dict()
    response['results'] = []
	pass		
```

`tasks.matching_task.matching_task.game_matching_task` 라는 이름의 Task 를 생성한다. 이 함수는 Socket.io 에서 호출될 것이다.

쌓여있는 대기열을 한 번에 수행하는 것이기 때문에, 중복되지 않는 여러 가지의 매치 정보가 존재한다. 그렇기 때문에 Dictionary 를 생성하고 `results` 라는 이름을 가진 배열을 넣어놓는다.

```python
if r.llen(match_queue_key_name) > 1:
	pass
else:
	return None
```

일정 시간동안 쌓인 큐의 길이가 적어도 2 이상이 아니라면 로직을 수행하는데 있어서 의미가 없다. 그렇기 때문에 분기로 나누어준다.

이제 조건에 부합할 때, 어떠한 로직을 작성했는지 보도록 한다.

```python
match_queue = r.lrange(name=match_queue_key_name, start=0, end=r.llen(match_queue_key_name))

for k, _ in groupby(match_queue, lambda x: x['id']):
	queue_by_id = list(_)
    
    try:
        if (len(queue_by_id) > 1):
            player_1, player_2, player_1_index, player_2_index = get_players(queue_by_id)
            
            redis_data = r.lindex(match_queue_key_name, index=player_1_index)
            redis_data_ = r.lindex(match_queue_key_name, index=player_2_index)

            r.lrem(match_queue_key_name, count=player_1_index, value=redis_data)
            r.lrem(match_queue_key_name, count=player_2_index, value=redis_data_)
            
            result_message = dict()

            # Get a players information, also build the response message
            response['results'].append(result_message)
            
    except Exception as e:
        # Whatever there is a exception, restore the item.
        pass
```

게임 매칭을 할 때, 조건 중 하나는 같은 문제집에 해당하는 Id 를 풀기 원하는 사용자 그룹 내에서 매칭을 해야하는 것이었다. 그렇기 때문에 groupby 를 통해 해당 리스트를 만들었다. 그 리스트의 길이가 2 이상일 때, 매칭을 해도 된다는 뜻이기 때문에 사용자의 정보를 Redis 에서 불러온 후, 메시지를 작성하고 Redis 에 있는 데이터를 삭제한다.

위의 코드 같은 경우에 가능한 매칭 완료가 가능한 최대 수는 반복문이 도는 수이다. 이를 개선할 수 있는 방법이 있다.

```python
for k, _ in groupby(match_queue, lambda x: x['id']):
	queue_by_id = list(_)
    try:
        for i in range(int(len(queue_by_id) / 2)):
            # Matching ...
            pass
    except Exception as e:
        # Whatever there is a exception, restore the item.
        pass
```

이 같은 경우에 **질문지의 수 * len(queue_by_id) / 2** 가 매칭 완료가 가능한 최대 수일 것이다.

이제 Socket.io 에서 어떻게 작성한 위의 Task 를 호출하는지 보도록 한다.

```python
if __name__ == '__main__':
    sio.start_background_task(callee_matching, sio, 5)
    web.run_app(app, port=port)
```

Python 용 Socket.io 라이브러리에서는 Background Task 를 위한 함수가 편의를 위해 제공되고 있다.

```Python
async def callee_task(sio, frequency=5):
    while True:
        await sio.sleep(frequency)

        print('Matching Celery Worker is always running for you ... :)')

        task = tasks.matching_task.game_matching_task.apply_async(())

        print('Celery Task Id -> {0}'.format(task))

        task_response = task.get()

        print('Celery Task Response -> {0}'.format(task_response))

        if task_response is not None:
            for i in task_response['results']:
                sio.enter_room(sid=i['user1Sid'], room=i['roomId'])
                sio.enter_room(sid=i['user2Sid'], room=i['roomId'])
                
                await sio.emit(event='match_status', data=response, room=i['roomId'])

async def callee_matching(sio, frequency=5):
    task_ = [callee_task(sio, frequency)]
    await asyncio.wait(task_)
```

Celery 에서는 여러 매치 정보를 결과로서 넘겨준다. 그 결과를 바탕으로 Room 생성 및 매치 결과를 각 사용자들에게 전달한다. 

> apply_async() 후, get(on_message=on_message) 를 통해서 결과를 전달하는 부분을 작성할 수 있을 것 같은데 어떻게 하는지 모르겠다.

### 5. Result

많은 생각을 하였음에도 불구하고, 실무에서는 더 심오한 시나리오들이 고려되고 있을 것이다. 이번 공부를 통해 겉으로 드러나는 것만 보기보다는 항상 좀 더 깊게 파봐야한다는 것을 배웠다.

> 혹시 틀린 내용이 있다면 언제든 Pull Request 혹은 Comment 를 해주시면 감사할 것 같다.

### 6. Reference 

http://docs.celeryproject.org/en/latest/