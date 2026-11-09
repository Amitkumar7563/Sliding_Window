# LeetCode 3: Longest Substring Without Repeating Characters

**Problem:** [LeetCode Link](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

**Difficulty:** Medium  
**Topics:** Sliding Window, Hashing, Two Pointers, String

---

## 1. Problem Understanding

Given a string `s`, find the length of the longest substring without repeating characters.

A substring means a continuous sequence of characters.

### Examples

**Example 1:**
```text
Input: s = "abcabcbb"
Output: 3
Explanation: "abc" is the longest substring.
```

**Example 2:**
```text
Input: s = "bbbbb"
Output: 1
Explanation: "b" is the longest substring.
```

**Example 3:**
```text
Input: s = "pwwkew"
Output: 3
Explanation: "wke" is the longest substring.
```

---

# 2. Brute Force Approach – O(N²)

### Intuition

Sabse pehle hum har possible starting index se substring banana start karenge.

For every starting index:

1. Create an empty `unordered_set`.
2. Traverse the string using another pointer.
3. Check whether the current character already exists.
4. If duplicate is found, break the loop.
5. Otherwise, insert the character into the set.
6. Update the maximum substring length.

### Why unordered_set?

Hume check karna hai ki koi character pehle se present hai ya nahi.

- `insert()` → Character add karta hai.
- `find()` → Character present hai ya nahi check karta hai.
- Average lookup time: O(1).

### C++ Code

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {

        int n = s.size();
        int ans = 0;

        for (int i = 0; i < n; i++) {

            unordered_set<char> st;

            for (int j = i; j < n; j++) {

                // Duplicate character found
                if (st.find(s[j]) != st.end()) {
                    break;
                }

                st.insert(s[j]);

                ans = max(ans, j - i + 1);
            }
        }

        return ans;
    }
};
```

### Complexity Analysis

- **Time Complexity:** O(N²) average case.
- **Space Complexity:** O(min(N, charset)).

---

# 3. Better Approach – Sliding Window + Unordered Set

### Intuition

Brute force mein hum baar-baar same characters ko check kar rahe hain.

Is unnecessary work ko avoid karne ke liye hum Sliding Window use karenge.

We maintain two pointers:

- `left` → Starting point of the window.
- `right` → Ending point of the window.

**Main Idea:**

Whenever a duplicate character is found, move `left` forward and remove characters from the set until the duplicate is removed.

### C++ Code

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {

        int n = s.size();
        int left = 0;
        int ans = 0;

        unordered_set<char> st;

        for (int right = 0; right < n; right++) {

            // Remove characters until duplicate is removed
            while (st.find(s[right]) != st.end()) {

                st.erase(s[left]);
                left++;
            }

            // Insert current character
            st.insert(s[right]);

            // Update maximum length
            ans = max(ans, right - left + 1);
        }

        return ans;
    }
};
```

### Complexity Analysis

- **Time Complexity:** O(N) average.
- **Space Complexity:** O(min(N, charset)).

### Why Sliding Window?

Instead of generating every substring again and again, we maintain a valid window and adjust its boundaries.

---

# 4. Optimal Approach – Sliding Window + Unordered Map

### Intuition

Better approach mein hum `unordered_set` use kar rahe the.

Lekin hum `unordered_map` use karke character ki frequency maintain kar sakte hain.

**Main Idea:**

1. `right` pointer se string traverse karenge.
2. Current character ki frequency increase karenge.
3. Agar frequency 1 se zyada ho gayi, toh duplicate present hai.
4. `left` pointer se characters remove karenge until duplicate resolve ho jaye.
5. Har valid window ki length calculate karenge.

### Why unordered_map?

```cpp
unordered_map<char, int> mp;
```

- `char` → Character store karega.
- `int` → Character ki frequency store karega.

Example:

```text
s = "abca"

Initially:
a = 1
b = 1
c = 1

After adding another 'a':
a = 2
b = 1
c = 1
```

Now we need to shrink the window.

### C++ Optimal Code

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {

        int left = 0;
        int ans = 0;

        unordered_map<char, int> mp;

        for (int right = 0; right < s.size(); right++) {

            mp[s[right]]++;

            // Remove repeated characters
            while (mp[s[right]] > 1) {

                mp[s[left]]--;

                if (mp[s[left]] == 0) {
                    mp.erase(s[left]);
                }

                left++;
            }

            // Update maximum length
            ans = max(ans, right - left + 1);
        }

        return ans;
    }
};
```

---

## 5. Important Code Explanation

### 1. Initialize pointers

```cpp
int left = 0;
int ans = 0;
```

- `left` maintains the starting index.
- `ans` stores the maximum valid substring length.

### 2. Frequency map

```cpp
unordered_map<char, int> mp;
```

Character ki frequency maintain karne ke liye use hota hai.

### 3. Expand the window

```cpp
mp[s[right]]++;
```

Right pointer current character ko window mein add karta hai.

### 4. Handle duplicates

```cpp
while (mp[s[right]] > 1)
```

Agar current character ki frequency 1 se zyada hai, toh duplicate mil gaya.

### 5. Shrink the window

```cpp
mp[s[left]]--;

if (mp[s[left]] == 0) {
    mp.erase(s[left]);
}

left++;
```

Leftmost character ki frequency decrease karte hain.

Agar frequency zero ho gayi, toh map se erase kar dete hain.

### 6. Calculate maximum length

```cpp
ans = max(ans, right - left + 1);
```

Window length formula:

```text
Length = right - left + 1
```

Both indexes are inclusive, so we add 1.

---

# 6. Dry Run – Optimal Approach

**Input:**
```text
s = "abcabcbb"
```

| Right | Character | Valid Window | Left | Ans |
|---|---|---|---|---|
| 0 | a | a | 0 | 1 |
| 1 | b | ab | 0 | 2 |
| 2 | c | abc | 0 | 3 |
| 3 | a | bca | 1 | 3 |
| 4 | b | cab | 2 | 3 |
| 5 | c | abc | 3 | 3 |
| 6 | b | cb | 5 | 3 |
| 7 | b | b | 7 | 3 |

**Final Answer = 3**

Longest substring = `"abc"`.

---

# 7. Brute vs Better vs Optimal

| Approach | Data Structure | Time | Space |
|---|---|---|---|
| Brute Force | Unordered Set | O(N²) | O(N) |
| Better | Unordered Set | O(N) | O(N) |
| Optimal (My Code) | Unordered Map | O(N) | O(N) |

*Note: Hash operations take O(1) average time.*

---

# 8. Important DSA Observations

### Q1. Why use `while` instead of `if`?

```cpp
while (mp[s[right]] > 1)
```

Because removing only one character may not resolve the duplicate.

We continue shrinking until the window becomes valid.

### Q2. Why use `right - left + 1`?

Because both indexes are inclusive.

Example:

```text
a b c
0 1 2

Length = 2 - 0 + 1 = 3
```

### Q3. Why erase when frequency becomes zero?

```cpp
if (mp[s[left]] == 0) {
    mp.erase(s[left]);
}
```

It removes characters that are no longer present in the current window.

### Q4. Why is Sliding Window O(N)?

- `right` moves forward at most N times.
- `left` also moves forward at most N times.
- Neither pointer moves backwards.

Total operations are O(2N) = O(N), assuming average O(1) hash operations.

---

# 9. Pattern Recognition – When to Use Sliding Window?

Look for these keywords:

- Longest substring.
- Shortest substring.
- Maximum/minimum subarray.
- Continuous elements.
- At most K distinct characters.
- No repeating characters.

Whenever a question asks for a continuous range with some condition, think about Sliding Window.

---

# 10. Revision Notes – Remember This

### Sliding Window Mental Model

**Expand → Check → Shrink → Update**

1. Expand window using `right`.
2. Check whether duplicate exists.
3. Shrink window using `left` until valid.
4. Update maximum length.

### Important Formula

```cpp
right - left + 1
```

### Important Data Structures

- `unordered_set` → Check whether an element exists.
- `unordered_map` → Maintain element frequency.

### Complexity

- Brute Force: O(N²)
- Sliding Window: O(N)
- Space: O(N)

### Most Important Learning

The key observation is that we don't need to generate every substring.

We only maintain a valid window and move its boundaries whenever a duplicate character appears.

---

# 11. Similar Problems for Practice

1. [Max Consecutive Ones III](https://leetcode.com/problems/max-consecutive-ones-iii/)
2. [Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/)
3. [Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/)
4. [Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)

**My Takeaway:** Understand why `left` moves and why we use `while` instead of `if`. These two observations are very important for solving variable-size Sliding Window problems.
