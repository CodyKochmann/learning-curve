# finally understanding asyncio (python3.6)

I've spent the last few months attempting to get asyncio to work in python 3.6 but couldnt seem to get things to work correctly. Finally, I was shown a snippet that I can work with so I figure its time to share.

Note: as always, yes you can split the invocations into multiple variables. But I believe YOU should determine when that is done based on the style of your code. This is simply the minimum code I've found is actually required to make something work.

```python
import asyncio

async def waffle(i):
    print('imma runnin', i)

asyncio.get_event_loop().run_until_complete(
    asyncio.gather(
        waffle(2),
        waffle(3)
    )
)
```
Output:
```
imma runnin 3
imma runnin 2
```
This also seems to be a good fit for splitting out generator expressions with the following.
```python
asyncio.get_event_loop().run_until_complete(
    asyncio.gather(
        *(waffle(i) for i in range(10))
    )
)
```
Output:
```
imma runnin 5
imma runnin 7
imma runnin 6
imma runnin 8
imma runnin 1
imma runnin 3
imma runnin 4
imma runnin 9
imma runnin 2
imma runnin 0
```
Awesome, things are actually working. So what if I didnt have all the operations ready to go immediately? What if I wanted to progressively queue a bunch of jobs for my code to run at the end or some predetermined "processing point"?
```python
>>> asyncio.gather(waffle(7))
>>> asyncio.gather(waffle(8))
>>> # do some unrelated stuff here
>>> # ...
>>> # pend another job to the queue just because
>>> asyncio.gather(waffle(9))
>>> # when we are actually ready to fire the queued jobs
>>> asyncio.get_event_loop().run_until_complete(asyncio.gather())
``` 
Output:
```
imma runnin 7
imma runnin 8
imma runnin 9
```