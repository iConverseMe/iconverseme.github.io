---
layout: post
title: "Running Median / Median of Integer Stream"
date: 2015-01-20 16:48:18 +0800
comments: true
categories: 
---
**Source**

[Median II](http://lintcode.com/en/problem/median-ii/#)

**Problem** 

Numbers keep coming, return the median of numbers at every time a new number added.

**Example**

For numbers coming list: [1, 2, 3, 4, 5], return [1, 1, 2, 2, 3]

For numbers coming list: [4, 5, 1, 3, 2, 6, 0], return [4, 4, 4, 3, 3, 3, 3]

For numbers coming list: [2, 20, 100], return [2, 2, 20]

**Challenge**

O(nlogn) time

**Clarification**

What's the definition of Median?

Median is the number that in the middle of a sorted array. If there are n numbers in a sorted array A, the median is A[(n-1)/2].
For example, if A=[1,2,3], median is 2. If A=[1,19], median is 1.

**Solution**

Use two heaps, a max heap and a min heap, for an integer i, if i smaller than the root of max heap, insert it to the max heap, or insert it to the min heap. Make sure the two requirements:

1. elements in max heap <= elements in min heap
2. |sizeOf(max heap) - sizeOf(min heap)| <= 1

Insert element and rejudge the heap needs O(lgn) time, and get median takes O(1) time.

```
public class Solution {
    /**
     * @param nums: A list of integers.
     * @return: the median of numbers
     */
    public int[] medianII(int[] nums) {
        // write your code here
        PriorityQueue<Integer> upperQueue = new PriorityQueue<Integer>();
        PriorityQueue<Integer> lowerQueue = new PriorityQueue<Integer>(20,new Comparator<Integer>(){
        	public int compare(Integer o1, Integer o2) {
        		return -o1.compareTo(o2);
        	}
        });
        
        //elements in upperQueue <= elememts in lowerQueue
        upperQueue.add(Integer.MAX_VALUE);
        lowerQueue.add(Integer.MIN_VALUE);
        int[] median = new int[nums.length];
        for(int i = 0; i < nums.length; i ++) {
            if(nums[i] >= lowerQueue.peek()) {
                upperQueue.add(nums[i]);
            } else {
                lowerQueue.add(nums[i]);
            }
            //load balance
            if(lowerQueue.size() - upperQueue.size() == 2) {
                upperQueue.add(lowerQueue.poll());
            } else if(upperQueue.size() - lowerQueue.size() == 2) {
                lowerQueue.add(upperQueue.poll());
            }
            if(lowerQueue.size() == upperQueue.size()) {
                // median[i] = (lowerQueue.peek() + upperQueue.peek())/2;
                median[i] = lowerQueue.peek();
            } else if(lowerQueue.size() > upperQueue.size()) {
                median[i] = lowerQueue.peek();
            } else {
                median[i] = upperQueue.peek();   
            }
        }
        return median;
    }
}

```

**Reference**

[http://www.dsalgo.com/2013/02/RunningMedian.php.html](http://www.dsalgo.com/2013/02/RunningMedian.php.html)
[http://www.ardendertat.com/2011/11/03/programming-interview-questions-13-median-of-integer-stream/](http://www.ardendertat.com/2011/11/03/programming-interview-questions-13-median-of-integer-stream/)
