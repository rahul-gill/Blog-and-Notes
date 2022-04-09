---
name: Order Statistics
updated: 2022-03-19 15:30:31Z
created: 2022-03-19 15:30:24Z
layout: note
categories: DSA
---


# Order statistics

- max and min can simply be find by traversing whole array $\mathcal{O}(n)$
- max & min both together can be found by taking elements of array in pairs, comparing the two elements, comparing smaller one to min & larger one to max: this will cost 3 comparison per 2 elements. so the number of comparisons become $3n/2$ instead on $2n$
- selecting $i^{th}$ order statistics
    - sort and return $i^{th}$ element $\mathcal{O}(n\lg n)$
    - we can use a heap: with STL, we'll create priority_queue with first i elements of array, then for other elements, if the element if smaller than pq's top then remove the top and push the element. After doing it for whole array the top of pq will be the answer. $\mathcal{O}(i + (n-i)\lg k)$: first building i-element heap with linear complexity then (n-k) push-pops with logorithmic complexity
	```cpp
	int tofindKthSmallest(vector<int> const &v, int k){
		priority_queue<int, vector<int>> pqueue(v.begin(), v.begin() + k);

		for (int i = k; i < v.size(); i++){	
			if (v[i] < pqueue.top()){
				pqueue.pop();
				pqueue.push(v[i]);
			}
		}
		return pqueue.top();
	}
	```
	- with heaps we can also find the smallest/largest $i$ elements of the array