---
layout:     post
title:      Algorithms daily 56
subtitle:   Leetcode 232 225 38 733 841 #副标题
date:       2019-04-27
author:     ASeeker
header-img: img/marvel_1.jpeg
catalog: true
tags:
    - Algorithms
    - Leetcode
    - Queue
    - Stack
    - Depth-first Search
    - String
    
---

## 此生无悔入漫威  
I love you three thousand  

>文尾稍有剧透 未观影者还是远离一下。。。

参考博客[^1] [^2]



## 232. Implement Queue using Stacks
>Implement the following operations of a queue using stacks.  
>See [***leetcode 232***][ref1] for details.   

[ref1]:https://leetcode-cn.com/problems/implement-queue-using-stacks/

***My Sol***   
简单粗暴 再来一个栈倒腾一下  


***Code***


```cpp
class MyQueue {
public:
    /** Initialize your data structure here. */
    MyQueue() {
        
    }
    
    /** Push element x to the back of queue. */
    void push(int x) {
        st.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    int pop() {
        
        stack<int> tempSt;
        while(!st.empty()){
        	int t = st.top();
        	st.pop();
        	tempSt.push(t);
        }
        int val = tempSt.top();
        tempSt.pop();
        while(!tempSt.empty()){
        	int t = tempSt.top();
        	tempSt.pop();
        	st.push(t);
        }
        return val;

    }
    
    /** Get the front element. */
    int peek() {
        stack<int> tempSt;
        while(!st.empty()){
        	int t = st.top();
        	st.pop();
        	tempSt.push(t);
        }
        int val = tempSt.top();
        while(!tempSt.empty()){
        	int t = tempSt.top();
        	tempSt.pop();
        	st.push(t);
        }
        return val;
    }
    
    /** Returns whether the queue is empty. */
    bool empty() {
        return st.empty();
    }

private:
	stack<int> st;
};
```

博客里给的第一种方法，也优化了一下，push的时候倒腾，pop和peek时直接，这样确实快了一点，毕竟如果回到全空，pop和peek次数肯定比push多  

这种思路很棒，两个栈new和old，push的时候塞到new里面，要peek和pop时倒腾到old里，old保存队列的样子，这样就不用来回来回倒腾啦  

```cpp
class MyQueue {
public:
    /** Initialize your data structure here. */
    MyQueue() {
        
    }
    
    /** Push element x to the back of queue. */
    void push(int x) {
        _new.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    int pop() {
        moveToOld();
        int val = _old.top();
        _old.pop();
        return val;
    }
    
    /** Get the front element. */
    int peek() {
        moveToOld();
        return _old.top();
    }
    
    /** Returns whether the queue is empty. */
    bool empty() {
        return _new.empty() && _old.empty();
    }


    void moveToOld(){
    	if(!_old.empty()){
    		return;
    	}else{
    		while(!_new.empty()){
    			int val = _new.top();
    			_new.pop();
    			_old.push(val);
    		}
    	}
    }

 private:
 	stack<int> _new, _old;

};
```

## 225. Implement Stack using Queues
>Implement the following operations of a stack using queues.  
>See [***leetcode 225***][ref2] for details.   

[ref2]:https://leetcode-cn.com/problems/implement-stack-using-queues/

***My Sol***   
push的时候便change，将最后入队的放到最前面  


***Code***

```cpp
class MyStack {
public:
    /** Initialize your data structure here. */
    MyStack() {
        
    }
    
    /** Push element x onto stack. */
    void push(int x) {
        q.push(x);
        for(int i = q.size(); i > 1; i--){
        	q.push(q.front());
        	q.pop();
        }
    }
    
    /** Removes the element on top of the stack and returns that element. */
    int pop() {
        int val = q.front();
        q.pop();
        return val;
    }
    
    /** Get the top element. */
    int top() {
        return q.front();
    }
    
    /** Returns whether the stack is empty. */
    bool empty() {
        return q.empty();
    }

private:
	queue<int> q;
};
```

## 38. Count and Say
>The count-and-say sequence is the sequence of integers with the first five terms as following:  
1.     1  
2.     11  
3.     21  
4.     1211  
5.     111221  
1 is read off as "one 1" or 11.  
11 is read off as "two 1s" or 21.  
21 is read off as "one 2, then one 1" or 1211.  
>See [***leetcode 38***][ref3] for details.   

[ref3]:https://leetcode-cn.com/problems/count-and-say/

***My Sol***   
intrersting  
根据数字个数往下走  
因为最多只可能连续出现3个相同的数，所以直接`count+'0'`  
搞清逻辑就好 


***Code***


```cpp
class Solution {
public:
    string countAndSay(int n) {
        
    	string res = "1";

    	int i = 1;
    	while(i < n){
    		string temp;
    		int j = 0, n = res.size();

    		while(j < n){
    			int count = 1;
    			while(j < n-1 && res[j] == res[j+1]){
    				j++;
    				count++;
    			}
    			temp += count + '0';
    			temp += res[j];
    			j++;
    		}
    		res = temp;
            i++;
    	}

    	return res;

    }
};
```

## 733. Flood Fill
>An image is represented by a 2-D array of integers, each integer representing the pixel value of the image (from 0 to 65535).  
Given a coordinate (sr, sc) representing the starting pixel (row and column) of the flood fill, and a pixel value newColor, "flood fill" the image.  
To perform a "flood fill", consider the starting pixel, plus any pixels connected 4-directionally to the starting pixel of the same color as the starting pixel, plus any pixels connected 4-directionally to those pixels (also with the same color as the starting pixel), and so on. Replace the color of all of the aforementioned pixels with the newColor.  
At the end, return the modified image.  
>See [***leetcode 733***][ref4] for details.   

[ref4]:https://leetcode-cn.com/problems/flood-fill/

***My Sol***   
bfs扩张即可  
注意newColor与原来的color相同的特殊情况   


***Code***

```cpp
class Solution {
public:
    vector<vector<int>> floodFill(vector<vector<int>>& image, int sr, int sc, int newColor) {
        
    	int oldColor = image[sr][sc];
        if(oldColor == newColor){
            return image;
        }
    	int m = image.size(), n = image[0].size();

    	queue<pair<int,int>> q;

    	q.push({sr,sc});
    	image[sr][sc] = newColor;

    	vector<pair<int,int>> directions({ {1,0}, {-1,0}, {0,1},{0,-1} });

    	while(!q.empty()){

    		for(int i = q.size(); i>0; --i){
    			auto a = q.front();
    			q.pop();

    			for(auto d :directions){
    				int x = a.first + d.first, y = a.second + d.second;
    				if(x >= 0 && x < m && y >= 0 && y < n && image[x][y] == oldColor){
    					image[x][y] = newColor;
    					q.push({x,y});
    				}
    			}
    		}

    	}

    	return image;
    }
};
```

## 841. Keys and Rooms
>There are N rooms and you start in room 0.  Each room has a distinct number in 0, 1, 2, ..., N-1, and each room may have some keys to access the next room.   
Formally, each room i has a list of keys rooms[i], and each key rooms[i][j] is an integer in [0, 1, ..., N-1] where N = rooms.length.  A key rooms[i][j] = v opens the room with number v.  
Initially, all the rooms start locked (except for room 0).   
You can walk back and forth between rooms freely.  
Return true if and only if you can enter every room.  
>See [***leetcode 841***][ref5] for details.   

[ref5]:https://leetcode-cn.com/problems/keys-and-rooms/

***My Sol***   
这道题被坑了两发提交。。。  
原来是到一个房间就可以拿走全部钥匙。。。   
傻傻的以为只能拿走一把钥匙  
可以拿走全部钥匙一下子就简单了很多，只要一个栈就可以了，判断最后剩下的房间数是不是0就ok啦


***Code***


```cpp
class Solution {
public:
    bool canVisitAllRooms(vector<vector<int>>& rooms) {
        int n = rooms.size();
        vector<bool> visited(n, false);
        stack<int> keys({0});

        while(!keys.empty()){
        	int key = keys.top();
        	keys.pop();
        	if(!visited[key]){
        		n--;
        		visited[key] = true;
        		for(auto a : rooms[key]){
        			keys.push(a);
        		}
        	}

        }

        return n==0;
        
    }

};
```

## 写在最后
终于去看了电影

完全猜错了灭霸的走向。。灭霸也是一黑到底

十一年的精心布局  
以tony为核心的主线终于画上了完美句号  
三个小时太短太短  
一个玩世不恭却又肩负起了所有的担当  
总是摆着高高在上的架子但内心也侠骨柔情 爱你不止三千遍  

明早还有课。。。早点睡觉了，日后填坑

……

Avengers！  
无所畏惧
 






[^1]: http://www.cnblogs.com/grandyang/p/4626238.html
[^2]: http://www.cnblogs.com/grandyang/p/4568796.html