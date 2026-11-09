# LeetCode 1004: Max Consecutive Ones III

**Topic:** Sliding Window + Two Pointers  
**Difficulty:** Medium  
**Approaches:** Brute Force → Better → Optimal

## 1. Problem Understanding

We are given a binary array `nums` containing only `0` and `1`, and an integer `k`.

We can flip at most `k` zeros into ones.

Our task is to find the **maximum length of a contiguous subarray containing only 1s after flipping at most k zeros.**

### Example

```cpp
nums = [1,1,1,0,0,0,1,1,1,1,0]
k = 2
```

**Output: 6**

Explanation:

We can flip two zeros in this subarray:

```cpp
[0,0,1,1,1,1]
```

After flipping:

```cpp
[1,1,1,1,1,1]
```

Maximum length = `6`.

---

## 2. Brute Force Approach

### Intuition

Generate all possible subarrays and count the number of zeros in each subarray.

- Fix the starting index `i`.
- Expand the ending index `j`.
- Whenever we encounter `0`, increment `maxzeros`.
- If `maxzeros <= k`, calculate the subarray length.
- If `maxzeros > k`, break because extending this subarray cannot make it valid again.

### Code

```cpp
class Solution {
public:
    int longestOnes(vector<int>& nums, int k) {
        int n = nums.size();
        int maxlen = 0;

        for(int i = 0; i < n; i++) {
            int maxzeros = 0;

            for(int j = i; j < n; j++) {

                if(nums[j] == 0)
                    maxzeros++;

                if(maxzeros <= k) {
                    int len = j - i + 1;
                    maxlen = max(maxlen, len);
                }
                else {
                    break;
                }
            }
        }

        return maxlen;
    }
};
```

### Complexity Analysis

- **Time Complexity:** O(N²) — We generate all possible subarrays.
- **Space Complexity:** O(1) — Only a few variables are used.

---

## 3. Better Approach – Sliding Window (Two Pointers)

### Intuition

Instead of generating every possible subarray, we maintain a valid window using two pointers:

- `left` → Starting point of the window.
- `right` → Ending point of the window.
- `zeros` → Number of zeros inside the current window.

### Important Observations

1. Expand the window by moving `right`.
2. If `nums[right] == 0`, increment `zeros`.
3. If `zeros > k`, the current window becomes invalid.
4. Shrink the window from the left using a `while` loop until `zeros <= k`.
5. Calculate the maximum valid window length.

**Window length formula:**

```cpp
len = right - left + 1;
```

### Code

```cpp
class Solution {
public:
    int longestOnes(vector<int>& nums, int k) {
        int n = nums.size();
        int maxlen = 0;
        int left = 0;
        int right = 0;
        int zeros = 0;

        while(right < n) {

            if(nums[right] == 0)
                zeros++;

            while(zeros > k) {

                if(nums[left] == 0)
                    zeros--;

                left++;
            }

            int len = right - left + 1;
            maxlen = max(maxlen, len);

            right++;
        }

        return maxlen;
    }
};
```

### Dry Run

```cpp
nums = [1,1,0,0,1,1,1]
k = 2
```

| right | nums[right] | zeros | left | Window length | maxlen |
|---:|---:|---:|---:|---:|---:|
| 0 | 1 | 0 | 0 | 1 | 1 |
| 1 | 1 | 0 | 0 | 2 | 2 |
| 2 | 0 | 1 | 0 | 3 | 3 |
| 3 | 0 | 2 | 0 | 4 | 4 |
| 4 | 1 | 2 | 0 | 5 | 5 |
| 5 | 1 | 2 | 0 | 6 | 6 |
| 6 | 1 | 2 | 0 | 7 | 7 |

**Answer = 7**

### Complexity Analysis

- **Time Complexity:** O(2N), which simplifies to O(N).
  - `right` traverses the array once.
  - `left` also traverses the array at most once.
- **Space Complexity:** O(1).

---

## 4. Optimal Approach – Sliding Window (Single Shrink)

### Intuition

In the Better approach, whenever `zeros > k`, we use a `while` loop to shrink the window until it becomes valid.

But do we really need to shrink the window completely every time?

**Observation:**

We only need to maintain the maximum valid window length.

If `zeros > k`, instead of repeatedly shrinking the window, we:

1. Check whether `nums[left]` is zero.
2. If yes, decrement `zeros`.
3. Move `left` one step forward.
4. Move `right` forward.

We use `if` instead of `while`.

This avoids repeatedly shrinking the window in a single iteration.

### Code

```cpp
class Solution {
public:
    int longestOnes(vector<int>& nums, int k) {
        int n = nums.size();

        int left = 0;
        int right = 0;
        int zeros = 0;
        int maxLen = 0;

        while(right < n) {

            if(nums[right] == 0)
                zeros++;

            if(zeros > k) {

                if(nums[left] == 0) {
                    zeros--;
                }

                left++;
            }

            if(zeros <= k) {
                int len = right - left + 1;
                maxLen = max(maxLen, len);
            }

            right++;
        }

        return maxLen;
    }
};
```

### Why `if` instead of `while`?

This is the most important observation in this problem.

**Better approach:**

```cpp
while(zeros > k) {
    if(nums[left] == 0)
        zeros--;

    left++;
}
```

It keeps shrinking until the window becomes valid.

**Optimal approach:**

```cpp
if(zeros > k) {
    if(nums[left] == 0)
        zeros--;

    left++;
}
```

It shrinks only once per iteration.

The key idea is that we don't need to find every valid window. We only need to find the maximum valid window length.

The current window can temporarily become invalid in the optimal approach. That's why we update `maxLen` only when `zeros <= k`.

### Complexity Analysis

- **Time Complexity:** O(N) — Both pointers move forward at most N times.
- **Space Complexity:** O(1) — No additional data structure is used.

---

## 5. Comparison of All Approaches

| Approach | Technique | Time Complexity | Space Complexity |
|---|---|---|---|
| Brute Force | Nested Loops | O(N²) | O(1) |
| Better | Sliding Window + While | O(N) | O(1) |
| Optimal | Sliding Window + If | O(N) | O(1) |

**Important:** Better and Optimal have the same asymptotic time complexity, O(N). The optimal version reduces repeated shrinking within an iteration; it is not a different Big-O complexity.

---

## 6. Most Important Learning / Takeaways

1. Whenever a problem asks for the longest contiguous subarray or substring, think about Sliding Window.
2. Use two pointers to maintain a window.
3. Maintain the count of the element that violates the condition (here, zeros).
4. `while` is useful when we need to restore a fully valid window.
5. `if` can be sufficient when we only need the maximum window length and can tolerate a temporarily invalid window.
6. Never forget the window length formula:

```cpp
right - left + 1
```

### Pattern to Remember

```cpp
while(right < n) {

    // Add the current element

    // If window becomes invalid, shrink it

    // Update answer

    right++;
}
```

---

## 7. My Key Observation

**The biggest learning from this question:**

In Sliding Window, we don't always need to maintain a completely valid window.

Sometimes, maintaining the maximum window size is enough.

This is the difference between the Better and Optimal approaches in this problem.

**Revision Keywords:** Sliding Window, Two Pointers, Variable Size Window, Maximum Length, Zero Counting, If vs While.
