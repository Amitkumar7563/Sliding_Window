# Longest Substring with At Most K Distinct Characters

**Problem:** [Take U Forward – Longest Substring with At Most K Distinct Characters](https://takeuforward.org/practice/dsa/longest-substring-with-at-most-k-distinct-characters?tab=problem)

**Pattern:** Sliding Window + Two Pointers + HashMap

**Goal:** Find the length of the longest substring containing at most `k` distinct characters.

---

## 1. Brute Force Approach

### Intuition
- Fix the starting index `i`.
- Expand the ending index `j`.
- Maintain character frequency using an `unordered_map`.
- If distinct characters are at most `k`, update maximum length.
- If distinct characters exceed `k`, stop expanding.

### Code

```cpp
class Solution {
public:
    int kDistinctChar(string& s, int k) {
        int n = s.length();
        int maxlen = 0;

        for(int i = 0; i < n; i++) {
            unordered_map<char, int> mpp;

            for(int j = i; j < n; j++) {
                mpp[s[j]]++;

                if(mpp.size() <= k) {
                    maxlen = max(maxlen, j - i + 1);
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

### Complexity
- Time: O(n²) expected
- Space: O(min(n, |Σ|))

---

## 2. Optimal Approach – Sliding Window + Two Pointers

### Intuition

Brute force mein hum har starting index ke liye naya substring explore karte hain.

Sliding Window mein hum previous window ko reuse karte hain.

We maintain:
- `left` → Window ka starting index.
- `right` → Window ka ending index.
- `mpp` → Character ki frequency store karta hai.
- `maxlen` → Maximum valid window length.

### Algorithm

1. `right` pointer se window expand karo.
2. Current character ki frequency map mein increase karo.
3. Agar distinct characters `k` se zyada ho jaayein, toh `left` pointer se window shrink karo.
4. Jab tak window valid nahi hoti (`mpp.size() <= k`), shrink karte raho.
5. Valid window ki length calculate karke `maxlen` update karo.

### Optimal C++ Code

```cpp
class Solution {
public:
    int kDistinctChar(string& s, int k) {
        int n = s.length();
        int left = 0;
        int maxlen = 0;

        unordered_map<char, int> mpp;

        for(int right = 0; right < n; right++) {

            // Expand window
            mpp[s[right]]++;

            // Shrink window if distinct > k
            while(mpp.size() > k) {

                mpp[s[left]]--;

                if(mpp[s[left]] == 0) {
                    mpp.erase(s[left]);
                }

                left++;
            }

            // Update maximum valid length
            maxlen = max(maxlen, right - left + 1);
        }

        return maxlen;
    }
};
```

### 3. Dry Run

Input:

```cpp
s = "aabacbebebe"
k = 3
```

| Window | Distinct characters | Status |
|---|---:|---|
| a | 1 | Valid |
| aa | 1 | Valid |
| aab | 2 | Valid |
| aaba | 2 | Valid |
| aabac | 3 | Valid |
| aabacb | 4 | Shrink |
| abac | 3 | Valid |
| acbe | 4 | Shrink |
| cbebe | 3 | Valid |

**Output: 5**

One longest valid substring is `"aabac"`.

---

## 4. Important Observations (Must Remember)

### Why `while` instead of `if`?

```cpp
while(mpp.size() > k)
```

Because window ko shrink karte waqt multiple characters remove karne pad sakte hain.

`if` sirf ek baar execute hoga, lekin `while` tab tak execute hota hai jab tak window valid nahi ho jaati.

### Why erase character when frequency becomes zero?

```cpp
if(mpp[s[left]] == 0) {
    mpp.erase(s[left]);
}
```

`mpp.size()` distinct characters count karta hai, total characters nahi.

Isliye zero frequency wale character ko erase karna zaroori hai.

### Why update maxlen after shrinking?

```cpp
maxlen = max(maxlen, right - left + 1);
```

Pehle window valid honi chahiye, uske baad hi maximum length update karni chahiye.

---

## 5. Complexity Analysis

| Approach | Time Complexity | Space Complexity |
|---|---|---|
| Brute Force | O(n²) expected | O(min(n, |Σ|)) |
| Optimal Sliding Window | O(n) expected | O(min(n, |Σ|)) |

**Why O(n) optimal?**

- `right` moves forward at most n times.
- `left` also moves forward at most n times.
- Each character enters and leaves the window at most once.

HashMap operations take O(1) expected time.

---

## 6. Pattern Recognition – When to Use This?

Sliding Window + Two Pointers is useful when:

- Question asks for longest/shortest substring.
- A condition must be maintained inside a window.
- We need at most K distinct elements/characters.
- We can expand and shrink the window without moving pointers backwards.

**Related Problems:**
1. Longest Substring Without Repeating Characters
2. Max Consecutive Ones III
3. Fruit Into Baskets
4. Longest Repeating Character Replacement
5. Subarrays with K Different Integers

---

## 7. Quick Revision

Remember this template:

```cpp
for(int right = 0; right < n; right++) {

    // 1. Expand window
    // Add current element

    // 2. Shrink invalid window
    while(condition_invalid) {
        // Remove left element
        left++;
    }

    // 3. Update answer
}
```

**Mental Model:**

Expand → Check Condition → Shrink → Update Answer

**Key Learning:** Sliding Window avoids recalculating overlapping subarrays by maintaining a continuously updated window.
