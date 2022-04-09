---
name: Array algorithms
updated: 2022-03-19 15:31:19Z
created: 2022-03-19 15:30:47Z
layout: note
categories: DSA
---

# Kadane's maximum sum subarray algorithm 
```cpp
int ans1, ans2;
int maxSubarraySum(int arr[], int n){
	int sum = 0, maxi = arr[0];
	int mssBegin=0, mssEnd=0, currentBegin=0;
	for(int i=0;i<n;i++){
		sum+=arr[i];
		if(sum>maxi){
			maxi = sum;
			mssBegin = currentBegin;
			mssEnd = i+1;
		}
		if(sum<0){
			sum = 0;
			currentBegin = i;
		}
	}
    ans1 = mssBegin;
    ans2 = mssEnd;
    return maxi;
}
```