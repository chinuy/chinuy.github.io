---
layout: post
title: LeetCode #55 Jump Game
tag: leetcode, code
---

[#55 Jump Game](https://leetcode.com/problems/jump-game/)

> Given an array of non-negative integers, you are initially positioned at the first index of the array.
  Each element in the array represents your maximum jump length at that position.
  Determine if you are able to reach the last index.
  For example:
  A = [2,3,1,1,4], return true.
  A = [3,2,1,0,4], return false.
  
My first two submissions were *Time Limit Exceeded* so I came up three types of algorithm for this problem.

## Naive thinking
Write a for-loop starting from A[0], each treat each *reachable* indexes as more starting points, comprehensively testing each possible point until the last element is reached.

For example, A = [2,3,1,1,4]. First iteration starts at A[0], which is 2, putting  3 and 1 as the second iteration starting points. Starting from A[1], it can reach to 1, 1, 4. Because **4** is the last element of the array, return `true` as the answer is found.

## Trick
The testing data is evil! It might be very long so the recursive solution is exhausted. It might be descending ordered, like `[2500, 2499, 2498, ..., 2, 1, 0]`, which leads to unnecessary computation and runtime exceeding. Don't think about skipping, for example A[0] = 2500 so I just need to check A[2500], because A[2500] might be 0 and A[2499] can be the one bridging to next number.

## First version

```c++
class Solution {
public:
    bool canJump(vector<int>& nums) {
        return subCanJump(nums.begin(), nums.end());
    }
        
    bool subCanJump(vector<int>::iterator i, vector<int>::iterator j) {
        if(j-i == 1)
            return true;
        
        for(int k = 1; k <= *i; ++k) {
            if(i+k < j && subCanJump(i+k, j))
                return true;
        }
        
        return false;
    }
};
```
A naive recursive solution. If `i` + `k` (jump) is less than the end of array, recursively find reachablity of `i+1` to `j`. Time complexity is O(n^2). Time wasted in duplicated jump distance calculation. Of course, recursive style itself is heavy compuational loading. Failed due to *Time Exceeded*.

## Second version

```c++
class Solution {
public:
    bool canJump(vector<int>& nums) {
        vector<bool> reached(nums.size(), false);
        reached[nums.size() -1] = true; // mark the last one as start point
        
        for(int i = nums.size()-2; i >=0; --i) {
            for(int r = 1; r <= nums[i]; ++r) {
                if(i + r > nums.size())
                    break;
                if(reached[i + r] == true) {
                    reached[i] = true;
                    break;
                }
            }
        }
        
        return reached[0]? true : false;
    }
};
```
Instead of starting from beginning, I was thinking maybe **starting from the end** would be better. Early break is possible when a reached point is found. Assumption was: if `A[n]` is reached, then `A[n-1]` can reach `A[n]` in just one testing, so the examing time is saved. However, still failed due to *Time Exceeded*, though more test passed.

## Third version

```c++
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int len = 0;
        
        for(int i = 0; i < nums.size(); ++i)
            if(len >= nums.size())
                return true;
            else if(len >= i)
                len = max(len, i + nums[i]);
            else
                return false;
            
        return len >= nums.size() -1 ? true : false;
    }
};
```
Turns out the solution is straightforwrd, scan from beginning, a `len` variable is used to record the max length (jump) of this array. Complexity O(n). This one is accepted.
