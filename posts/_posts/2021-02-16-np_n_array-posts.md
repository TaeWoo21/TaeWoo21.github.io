---
layout: post
categories: posts
title: "[NumPy] 배열 N차원 배열 다루기"
comments: true
---

이 문서에서는 NumPy 모듈을 이용하여 N차원 배열을 다루는 방법에 대하여 설명한다. 또한 이 문서에 포함되어 있는 내용은 작성일(2021-02-16) 기준 내용이므로 참고하길 바란다.


## [ 배열 합치기 ] 

NumPy 모듈을 이용하여 N차원 배열을 합치는 다양한 방법

- 두 배열을 위에서 아래로 붙이기
   - np.vstack((a,b))	
   - np.row_stack((a,b))
   - np.r_[a,b]
   - np.concatenate((a,b), axis=1)
- 두 배열을 왼쪽에서 오른쪽으로 붙이기	
	- np.hstack((a,b))
	- np.column_stack((a,b))
	- np.c_[a,b]
	- np.concatenate((a,b), axis=0)


### np.vstack()


```python
>>> a = np.array([1, 2, 3])
>>> b = np.array([4, 5, 6])
>>> np.vstack((a,b))
-------------------------------------------
array([[1, 2, 3],
       [4, 5, 6]])
```
```python
# a : array([[0, 1, 2],
#            [3, 4, 5]])
# b : array([[ 6,  7,  8],
#            [ 9, 10, 11]])
>>> a = np.arange(6).reshape((2,3))   
>>> b = np.arange(6,12).reshape((2,3))    
>>> np.vstack((a,b))
-------------------------------------------
array([[ 0,  1,  2],
       [ 3,  4,  5],
       [ 6,  7,  8],
       [ 9, 10, 11]])
```


### np.row_stack()

np.vstack() 결과와 동일



### np.r_

```python
>>> a = np.array([1, 2, 3])
>>> b = np.array([4, 5, 6])
>>> np.r_[a,b]
-------------------------------------------
array([1, 2, 3, 4, 5, 6])
```
```python
# 차원 하나가 증가해서 붙음
>>> a = np.array([1, 2, 3])
>>> b = np.array([4, 5, 6])
>>> np.r_[[a],[b]]  
-------------------------------------------
array([[1, 2, 3],
       [4, 5, 6]])
```
```python
# a : array([[0, 1, 2],
#            [3, 4, 5]])
# b : array([[ 6,  7,  8],
#            [ 9, 10, 11]])
>>> a = np.arange(6).reshape((2,3))   
>>> b = np.arange(6,12).reshape((2,3))    
>>> np.r_[a,b]    # shape: (4, 3)
-------------------------------------------
array([[ 0,  1,  2],
       [ 3,  4,  5],
       [ 6,  7,  8],
       [ 9, 10, 11]])
```
```python
# a : array([[0, 1, 2],
#            [3, 4, 5]])
# b : array([[ 6,  7,  8],
#            [ 9, 10, 11]])
# 차원 하나가 증가해서 붙음
>>> a = np.arange(6).reshape((2,3))    
>>> b = np.arange(6,12).reshape((2,3))   
>>> np.r_[[a],[b]]    # shape: (2, 2, 3)
-------------------------------------------
array([[[ 0,  1,  2],
        [ 3,  4,  5]],

       [[ 6,  7,  8],
        [ 9, 10, 11]]])
```


### np.hstack()

```python
>>> a = np.array([1, 2, 3])
>>> b = np.array([4, 5, 6])
>>> np.hstack[a,b]
-------------------------------------------
array([1, 2, 3, 4, 5, 6])
```
```python
# a : array([[0, 1, 2],
#            [3, 4, 5]])
# b : array([[ 6,  7,  8],
#            [ 9, 10, 11]])
>>> a = np.arange(6).reshape((2,3))    
>>> b = np.arange(6,12).reshape((2,3))   
>>> np.hstack([a,b])
-------------------------------------------
array([[ 0,  1,  2,  6,  7,  8],
       [ 3,  4,  5,  9, 10, 11]])      											   
```


### np.column_stack()

```python
>>> a = np.array([1, 2, 3])
>>> b = np.array([4, 5, 6])
>>> np.column_stack((a,b))
-------------------------------------------
array([[1, 2],
       [2, 3],
       [3, 4]])
```
```python
# a : array([[0, 1, 2],
#            [3, 4, 5]])
# b : array([[ 6,  7,  8],
#            [ 9, 10, 11]])
>>> a = np.arange(6).reshape((2,3))
>>> b = np.arange(6,12).reshape((2,3))
>>> np.column_stack((a,b))
-------------------------------------------
array([[ 0,  1,  2,  6,  7,  8],
       [ 3,  4,  5,  9, 10, 11]])
```


### np.c_
```python
>>> a = np.array([1, 2, 3])
>>> b = np.array([4, 5, 6])
>>> np.c_[a,b]
-------------------------------------------
array([[1, 2],
       [2, 3],
       [3, 4]])
```
```python
# a : array([[0, 1, 2],
#            [3, 4, 5]])
# b : array([[ 6,  7,  8],
#            [ 9, 10, 11]])
>>> a = np.arange(6).reshape((2,3))    
>>> b = np.arange(6,12).reshape((2,3))    
>>> np.c_[a,b]    # shape: (4, 3)
-------------------------------------------
array([[ 0,  1,  2,  6,  7,  8],
       [ 3,  4,  5,  9, 10, 11]])
```


### np.concatenate()

```python
>>> a = np.array([1, 2, 3])
>>> b = np.array([4, 5, 6])
>>> np.concatenate((a,b), axis=0)
-------------------------------------------
array([1, 2, 3, 4, 5, 6])
```
```python
# a : array([[0, 1, 2],
#            [3, 4, 5]])
# b : array([[ 6,  7,  8],
#            [ 9, 10, 11]])
>>> a = np.arange(6).reshape((2,3))    
>>> b = np.arange(6,12).reshape((2,3))    
>>> np.concatenate((a,b), axis=0)
-------------------------------------------
array([[ 0,  1,  2],
       [ 3,  4,  5],
       [ 6,  7,  8],
       [ 9, 10, 11]])
```
```python
# a : array([[0, 1, 2],
#            [3, 4, 5]])
# b : array([[ 6,  7,  8],
#            [ 9, 10, 11]])
>>> a = np.arange(6).reshape((2,3))
>>> b = np.arange(6,12).reshape((2,3))
>>> np.concatenate((a,b), axis=1)
-------------------------------------------
array([[ 0,  1,  2,  6,  7,  8],
       [ 3,  4,  5,  9, 10, 11]])
```


## Reference
- [https://rfriend.tistory.com/352](https://rfriend.tistory.com/352)
