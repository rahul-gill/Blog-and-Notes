---
name: Sorting alogorithms
updated: 2022-03-19 15:30:09Z
created: 2022-03-19 15:29:12Z
layout: note
categories: DSA
---

# Quick Sort

- worst case $\mathcal{O}(n^2)$ but the average case $\mathcal{O}(\lg n)$; winner in practice
- in-place & unstable sorting
- divide and conquer strategy for sorting `A[p...r]`
    - partition the array in subarrays `A[p...q-1]` and `A[q+1...r]` such that first subarray contains elements smaller than or equal to `A[q]` and second contains greater than `A[q]`
    - sort the subarrays recursively
    - no need to combine the results

```cpp
void quick_sort(vector<int> &arr, int l = 0,int r = INT_MIN){
    if(r == INT_MIN) r = arr.size(); // can't be done as function argument
    if(l >= r) return;
    int m = partition(arr,l,r);
    quick_sort(arr, l, m );
    quick_sort(arr, m + 1, r);
}
```

- random partitioning

```cpp
int partition(vector<int> &arr, int l,int r){
    int ramdom_index = l + (rand() % (r-l));
    swap(arr[ramdom_index], arr[r-1]);
    int pivot = arr[r-1];
    int i = l;
    for(int j = l; j<r-1;j++){
        if(arr[j] <= pivot){
            swap(arr[i], arr[j]);
            i++;
        }
    }
    swap(arr[r-1], arr[i]);
    return i;
}
```

![8d66cb094060a76bba4e629d7ad6f4d3.png](../_resources/8d66cb094060a76bba4e629d7ad6f4d3.png)

## Performance analysis

- worst-case paritioning: when one subproblem have 0 elements and other have n-1(array is already sorted) then

$$
T(n) = T(n-1) + T(0) + \Theta(n) = \Theta(n^2)

$$

- best-case

$$
T(n) =  	2T(n/2) + \Theta(n) = \Theta(n\lg n)

$$

- you'll see that even if partitions have size ratios 1:9, the running time is $\mathcal{O}(n\lg n)$ and there is more than 80% chance of the partitioning being better than the 1:9 ratio
- expected running time if one partition contain q elements

$$
T(n) =   T(q) + T(n-q-1) + \Theta(n)

$$

parition method is called atmost n time and if the number of comparisons done in line `arr[j] <= pivot` are $X$ then runtime of quick sort is $\mathcal{O}(n + X)$

- overall bound on $X$

two elements are compared at most once because once an element is compared to the pivot in some partitioning it won't be compared again

$$
\text{let indicator r.v. } X_{ij} =(\text{is } A_i \text{ compared to } A_j)\\
X = \sum\limits_{i=1}^{n-1} \sum\limits_{j=i+1}^{n} X_{ij}\\
\implies E[X] = \sum\limits_{i=1}^{n-1} \sum\limits_{j=i+1}^{n}E[ X_{ij}]\\

$$

once a pivot is x chosen and $A_i<x<A_j$ then its sure that $A_i$ and $A_j$ won't be compared. so $A_i$ and $A_j$ are compared if and only if first element chosen from $\{A_i, A_{i+1},...,A_j\}$ is either $A_i$ or $A_j$ so

$$
E[X_{ij}] =P(\text{is } A_i \text{ compared to } A_j)\\
= P(z_i \text{ or } z_j  \text{  is first pivot chosen from } \{A_i, A_{i+1},...,A_j\})\\
\ \\
\text{since all items are equally likey to be chosen so:}\\
E[X_{ij}] = \frac{2}{j-i+1}\\
\implies E[X] = \sum\limits_{i=1}^{n-1} \sum\limits_{j=i+1}^{n} \frac{2}{j-i+1} \\
\ \\
\text{let } k  = j - i:\\
E[X] = \sum\limits_{i=1}^{n-1} \sum\limits_{k=1}^{n-i} \frac{2}{k+1} \lt 
\sum\limits_{i=1}^{n-1} \sum\limits_{k=1}^{n} \frac{2}{k} \\
\implies E[X] = \sum\limits_{i=1}^{n-1} \mathcal{O} (\lg n)\\
\implies E[X] = \mathcal{O} (n\lg n)

$$

# Linear Time sorting

- non-comparison sorts(sorting order won't be determined by comparing keys of elements)

## Counting Sort

- assumes that keys of elements are in range 0 to k, cost $\mathcal{O}(n+k)$. When $k=\mathcal{O}(n)$, time complexity is also $\mathcal{O}(n)$

```cpp
//sorting array containing only integer keys, max_key exclusive
vector<int> rapid_sort(const vector<int> &arr, int max_key){
    vector<int> count(max_key, 0), ret_array(arr.size());
    for(int i=0; i<arr.size(); ++i) ++count[arr[i]];

    int indx = 0;
    for(int i=0; i<=max_key; ++i)
        for(int j=1; j<=count[i]; ++j)
            ret_arr[indx++] = i;
    return ret_arr;
}
//sorting array containing integer keys and additional information
//somethigs can be changed to general type T and T.key
vector<int> count_sort(const vector<int> &arr, int max_key){
    vector<int> count(max_key, 0),ret_arr(arr.size());
    for(int i=0; i<arr.size(); ++i) ++count[arr[i]];
    for(int i=1; i<=max_key; ++i) count[i] += count[i - 1];
    
    for (int i = arr.size()-1; i>=0; --i){
        ret_arr[count[arr[i]]-1] = arr[i];
        --count[arr[i]];
    }
    return ret_arr;
}
```

## Radix Sort

| Intially | run I | run II | run III |
| --- | --- | --- | --- |
| 329 | 72 $0$ | 7 $2$ 0 | $3$ 29 |
| 457 | 35 $5$ | 3 $2$ 9 | $3$ 55 |
| 657 | 43 $6$ | 4 $3$ 6 | $4$ 36 |
| 839 | 45 $7$ | 8 $3$ 9 | $4$ 57 |
| 436 | 65 $7$ | 3 $5$ 5 | $6$ 57 |
| 720 | 32 $9$ | 4 $5$ 7 | $7$ 20 |
| 355 | 83 $9$ | 6 $5$ 7 | $8$ 39 |

- for n d-digit numbers with each digit having k possible values, RadixSort takes $\mathcal{O}(d(n+k))$ if the stable sort its using is $\mathcal{O}(n+k)$
- when d is constant and k is of order n, then it sots in linear time

```cpp
//modified count sort
void count_sort(vector<int> &arr, int max_key,int factor){
    vector<int> count(max_key, 0);
    vector<int> ret_arr(arr.size(),0);

    for(int i=0; i< arr.size(); ++i){
        auto x = (arr[i]/factor) % 10;
     	count[x]++;
    }
    for(int i=1; i<max_key; ++i) 
     	count[i] += count[i - 1];

    for (int i = arr.size()-1; i>=0; --i){
        auto x = (arr[i]/factor) % 10;
        ret_arr[count[x] - 1 ] = arr[i];
        count[x]--;
    }
    arr = ret_arr;
}
void radix_sort(vector<int> &arr){
    int max_element = INT_MIN;
    for(auto x:arr) if(x > max_element) max_element = x;
    
    for(int i=1; max_element/i > 0; i*=10)
        count_sort(arr, 10,i);
}
```

## Bucket sort

- it assumes that input is from a uniform distribution
- have average case running time $\mathcal{O}(n)$
- this implementation for range $[0,1)$

```cpp
void bucket_sort(vector<double> &arr){
    vector<vector<double>> buckets(arr.size());
    int n = arr.size();
    for(int i=0; i<n; ++i){
        int bucket_index = n * arr[i];
        buckets[bucket_index].push_back(arr[i]);
    }
    //insertion sort will also be ok
    for(int i=0; i<n; ++i) sort(buckets[i].begin(), buckets[i].end());
    
    int index = 0;
    for(int i=0; i<n; ++i)
        for(int j=0; j<buckets[i].size(); j++)
            arr[index++] = buckets[i][j];
}
```

- for general range
    - calculate `max_element` and `min_element` of array and `span = (max-min)/n`
    - change `bucket_index = (arr[i] - min_element) / span`
- analysis

$$
\text{let r.v. } n_i = \text{ number of elements in bucket } i\\
\text{so } T(n) = \Theta(n) + \sum\limits_{i=0}^{n-1}\mathcal{O}(n_i^2)\\
E[X] = \Theta(n) + \sum\limits_{i=0}^{n-1}\mathcal{O}\left(E[n_i^2]\right)\\
\text{now for calculating }E[n_i^2] \text{ let indicator r.v. } X_{ij} = A[j] \text{ falls in bucket i then}\\
n_i = \sum\limits_{j=0}^{n} X_{ij}
E[n_i^2] = E\left[\left( \sum\limits_{j=0}^{n-1} X_{ij} \right)^2 \right]\\
= \sum\limits_{j=0}^{n-1}E[X_{ij}^2] + \mathop{\sum\limits_{j = 0}^{n} \sum\limits_{k=0}^n }\limits_{k\neq j} E[X_{ij}X_{ik}]\\
X_{ij} \text{ is 1 with probability } 1/n  \text{ and 0 otherwise so}\\
E[X_{ij}^2] = 1^2\cdot (1/n) + 0^2(1-1/n) = 1/n\\
\text{ when } k\neq j, X_{ij} \text{ and } X_{ij} \text{ are independent, so}\\
E[X_{ij}X_{ij}] = 1/n^2\\
\implies E[n_i^2] = \sum\limits_{j=0}^{n-1}{\frac{1}{n}} + \mathop{\sum\limits_{j = 0}^{n} \sum\limits_{k=0}^n }\limits_{k\neq j} \frac{1}{n^2}\\
= 2 -\frac{1}{n}\\
\ \\
\implies E[X] = \Theta(n) + n\cdot \mathcal{O}(2-1/n) = \Theta(n) 

$$


