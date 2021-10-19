---
layout: default
name: _notes/dsa/CP Cheatsheet.md
categories: DSA
date: 2021-10-19
---
<script 
    type="text/javascript"
    src="https://unpkg.com/mermaid@8.13.2/dist/mermaid.min.js">
</script>

<link 
  rel="stylesheet" 
  href="https://cdn.jsdelivr.net/npm/katex@0.13.18/dist/katex.min.css" integrity="sha384-zTROYFVGOfTw7JV7KUu8udsvW2fx4lWOsCEDqhBreBwlHI4ioVRtmIvEThzJHGET" crossorigin="anonymous">

<script defer 
  src="https://cdn.jsdelivr.net/npm/katex@0.13.18/dist/katex.min.js" integrity="sha384-GxNFqL3r9uRJQhR+47eDxuPoNE7yLftQM8LcxzgS4HT73tp970WS/wV5p8UzCOmb" crossorigin="anonymous">
</script>

<script defer 
  src="https://cdn.jsdelivr.net/npm/katex@0.13.18/dist/contrib/auto-render.min.js" integrity="sha384-vZTG03m+2yp6N6BNi5iM4rW4oIwk5DfcNdFfxkk9ZWpDriOkXX8voJBFrAO7MpVl" crossorigin="anonymous">
</script>
<script>
    document.addEventListener("DOMContentLoaded", function() {
        renderMathInElement(document.body, {
          // customised options
          // • auto-render specific keys, e.g.:
          delimiters: [
              {left: '$$', right: '$$', display: true},
              {left: '$', right: '$', display: false}
          ],
          // • rendering keys, e.g.:
          throwOnError : false
        });
    });
</script>
### My Map of Things

- [gfg link](https://www.geeksforgeeks.org/data-structures/)

<div class="mermaid">graph LR;
    subgraph basics1
        subgraph array
            a1(array rotations)
            a2(array rearrangment)
            a3(order statistics)
        end
        subgraph linked_list
            l1(singly, doubly, circular)
        end
        stack
        queue
    end
    
    subgraph basics2
        subgraph bst
            implementation
            more
        end
        subgraph heap
            binomial
            fibbonaci
        end
        subgraph hashing
            hs1("open addressing and chaining")
            more
        end
        subgraph graph
            gr("DFS BFS and usecases")
        end
    end
    
    subgraph advanced
        adv("advanced lists")
        adv2("segment_tree, bit tree, union find")
        trie
        adv4("suffix array, tree")
    end
    

</div>
- range queries



# Cheatsheet

- cout modifiers
	- bases  `basefield`: dec, hex, oct(for binary do bitset<32>(int_variable))
	- float format `floatfield`: fixed, scietific
	- `adjustfield`: internal, left, right
	- independent flags: `skipws`
```c++
cout.flags(ios::right | ios::hex | ios::showbase)
```

- arrays

```c++
fill_n(arr_begin, arr_size, value_to_fill);
copy(arr_begin, arr_end, result_arr_begin);
reverse(arr_begin, arr_end);

sort(arr_begin, arr_end);
sort(arr_begin, arr_end, [](int a, int b){return a>b;})	//descending sort
//partial sorting: arr_begin to arr_middle will be sorted and contain smallest elements of array
partial_sort(arr_begin, arr_middle, arr_end);	
//binary search: return iterator to first position(upper_bound return last position) at which value can be inserted without disturbing the ordering
lower_bound(arr_begin, arr_end, value);	

next_permutation(arr_begin, arr_end)
//unique elements
vector<int>A = {1,2,3,1,2,5};
sort(A.begin(),A.end());
//unique makes the array {1,2,3,5,*,*} and return iterator to first *(duplicate)
A.erase(unique(A.begin(),A.end()),A.end());

transform(input_begin, input_end,output_begin,operation_function)
swap(arr_begin, arr_end, result_arr_begin);
random_suffle(arr_begin, arr_end);
```

- c char library functions

```
islower, isupper, isalpha, isdigit, isalnum etc.
```

- bit manipulation

```
#define isOn(S, j) (S & (1<<j))
#define setBit(S, j) (S |= (1<<j))
#define clearBit(S, j) (S &= ~(1<<j))
#define toggleBit(S, j) (S ^= (1<<j))
#define lowestOnBit(S) (S & (-S))	//lowest non-zero bit
#define setAll(S, n) (S = (1<<n)-1)

#define modulo(S, N) ((S) & (N-1))   // returns S % N, where N is a power of 2
#define isPowerOfTwo(S) (!(S & (S-1)))
#define nearestPowerOfTwo(S) (1<<lround(log2(S)))
#define turnOffLastBit(S) ((S) & (S-1))
#define turnOnLastZero(S) ((S) | (S+1))
#define turnOffLastConsecutiveBits(S) ((S) & (S+1))
#define turnOnLastConsecutiveZeroes(S) ((S) | (S-1))
```

- stack, queue, deque and priority_queue
```c++
stack/priority_queue<int> s;s.push('a'); s.pop(); s.empty(); s.top()
queue<char> s; s.push('a'); s.pop(); s.empty(); s.front(); s.back()
deque<char> s; push_back,pop_back,push_front,pop_front,front,back,empty
```
- map/set and their muli and unordered variants
```c++
s.insert(3); s.find(3); s.erase(3); s.lower_bound(3); //etc.
```
