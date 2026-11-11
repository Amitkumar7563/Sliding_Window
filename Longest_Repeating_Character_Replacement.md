# 424. Longest Repeating Character Replacement

**LeetCode:** [Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/)

**Topic:** Sliding Window + Two Pointers + Hashing  
**Difficulty:** Medium  
**Pattern:** Longest Substring with At Most K Changes

---

## 1. Problem Understanding

Given a string `s` containing uppercase English letters and an integer `k`.

We can replace at most `k` characters in the string with any uppercase English letter.

Return the length of the longest substring containing the same character after at most `k` replacements.

### Example

```text
Input: s = "AABABBA", k = 1
Output: 4
```

**Explanation:**

```text
"AABA"

A A B A
```

Replace `B` with `A`.

The resulting substring is `"AAAA"`.

Therefore, the answer is `4`.

---

## 2. Important Observation

To make all characters in a window equal, we should keep the most frequent character and replace all other characters.

### Formula

```text
Window Length = r - l + 1

Minimum Changes = Window Length - Maximum Frequency
```

Therefore:

```cpp
int changes = (r - l + 1) - maxf;
```

**Valid Window:**

```cpp
changes <= k
```

**Invalid Window:**

```cpp
changes > k
```

Where:
- `l` = Left pointer
- `r` = Right pointer
- `maxf` = Maximum frequency of a character in the window
- `k` = Maximum allowed replacements

---

# 3. Approach 1: Brute Force

### Intuition

Generate all possible substrings and check how many replacements are required to make every character equal.

### Steps

1. Use the outer loop to select the starting index `i`.
2. Initialize `hash[26]` and `maxf = 0`.
3. Use the inner loop to expand the substring.
4. Update character frequency.
5. Update maximum frequency.
6. Calculate minimum replacements.
7. If replacements are within `k`, update the answer.
8. Otherwise, break because extending this substring cannot make it valid again.

### Code

```cpp
class Solution {
public:
    int characterReplacement(string s, int k) {
        int n = s.length();
        int maxLen = 0;

        for(int i = 0; i < n; i++) {
            int hash[26] = {0};
            int maxf = 0;

            for(int j = i; j < n; j++) {

                hash[s[j] - 'A']++;

                maxf = max(maxf, hash[s[j] - 'A']);

                int change = (j - i + 1) - maxf;

                if(change <= k) {
                    maxLen = max(maxLen, j - i + 1);
                }
                else {
                    break;
                }
            }
        }

        return maxLen;
    }
};
```

### Complexity Analysis

- **Time:** O(N²) — 26 is a constant.
- **Space:** O(26) = O(1) — frequency array.

### Limitation

We repeatedly generate overlapping substrings. We can optimize this using Sliding Window.

---

# 4. Approach 2: Better — Sliding Window + While

### Intuition

Instead of generating every substring, maintain a window using two pointers.

- `r` expands the window.
- `l` shrinks the window when it becomes invalid.
- `hash[26]` maintains character frequencies.

### Steps

1. Initialize `l = 0`, `r = 0`, `maxLen = 0`, `maxf = 0`.
2. Expand the window by including `s[r]`.
3. Update its frequency and `maxf`.
4. If the window becomes invalid, shrink it using `while`.
5. Decrease the frequency of the character at `l`.
6. Recalculate `maxf` by traversing all 26 characters.
7. Move `l` forward.
8. Update the maximum window length.
9. Move `r` forward.

### Code

```cpp
class Solution {
public:
    int characterReplacement(string s, int k) {
        int n = s.length();

        int l = 0, r = 0;
        int maxLen = 0;
        int maxf = 0;

        int hash[26] = {0};

        while(r < n) {

            hash[s[r] - 'A']++;

            maxf = max(maxf, hash[s[r] - 'A']);

            // Shrink until window becomes valid
            while((r - l + 1) - maxf > k) {

                hash[s[l] - 'A']--;

                maxf = 0;

                // Recalculate maximum frequency
                for(int i = 0; i < 26; i++) {
                    maxf = max(maxf, hash[i]);
                }

                l++;
            }

            maxLen = max(maxLen, r - l + 1);

            r++;
        }

        return maxLen;
    }
};
```

### Why do we recalculate `maxf`?

When we remove a character from the left side, its frequency may decrease.

Example:

```text
Before removing:

A = 3
B = 2

maxf = 3
```

After removing one `A`:

```text
A = 2
B = 2

maxf = 2
```

Therefore, we recalculate the maximum frequency.

### Complexity Analysis

- **Time:** O(26N) = O(N).
- **Space:** O(26) = O(1).

### Limitation

We repeatedly traverse the frequency array to recalculate `maxf`.

Can we eliminate this repeated calculation?

Yes. This leads to the optimal approach.

---

# 5. Approach 3: Optimal — Sliding Window + If

### Main Observation

In the better approach, we shrink the window using `while` until it becomes valid.

But in the optimal approach, we only shrink the window once using `if`.

**Why?**

Our objective is to find the maximum window length, not necessarily to make every intermediate window valid.

We can maintain the maximum length without shrinking repeatedly.

### Important Difference

| Better | Optimal |
|---|---|
| Uses `while` | Uses `if` |
| Shrinks until valid | Shrinks at most once |
| Recalculates `maxf` | Does not recalculate `maxf` |
| Maintains a valid window | May temporarily maintain an invalid window |

### Code

```cpp
class Solution {
public:
    int characterReplacement(string s, int k) {
        int n = s.length();

        int l = 0, r = 0;
        int maxLen = 0;
        int maxf = 0;

        int hash[26] = {0};

        while(r < n) {

            hash[s[r] - 'A']++;

            maxf = max(maxf, hash[s[r] - 'A']);

            // Shrink only once if window becomes invalid
            if((r - l + 1) - maxf > k) {

                hash[s[l] - 'A']--;
                l++;
            }

            maxLen = max(maxLen, r - l + 1);

            r++;
        }

        return maxLen;
    }
};
```

---

## 6. Why Does `if` Work Instead of `while`?

This is the most important concept in this problem.

### Better Approach

When the window becomes invalid:

```cpp
while(windowLength - maxf > k) {
    // Shrink the window
}
```

We keep shrinking until the window becomes valid.

### Optimal Approach

```cpp
if(windowLength - maxf > k) {
    // Shrink only once
}
```

We do not need to restore validity after every expansion.

**Reasoning:**

1. The right pointer expands the window by one position.
2. If the window becomes invalid, the left pointer also moves forward by one position.
3. Therefore, the window length does not increase during that iteration.
4. We only need to track the maximum length achieved so far.
5. The algorithm never increases its recorded window length based on a stale maximum frequency to produce an incorrect answer.

The optimal approach maintains the maximum possible length, rather than insisting that every intermediate window must be valid.

---

## 7. Why Don't We Recalculate `maxf` in the Optimal Approach?

This is another important observation.

Consider:

```text
Window frequencies:

A = 3
B = 2

maxf = 3
```

After removing one `A`:

```text
A = 2
B = 2

Actual maximum frequency = 2
Stored maxf = 3
```

Now `maxf` is greater than the actual maximum frequency of the current window.

This is called a **stale maximum frequency**.

We intentionally do not decrease `maxf`.

```cpp
maxf = max(maxf, hash[s[r] - 'A']);
```

### Why is this safe?

- `maxf` represents the highest frequency observed during the expanding window.
- It does not necessarily represent the exact maximum frequency in the current window.
- A stale `maxf` can make the current window appear valid even if its actual replacement count exceeds `k`.
- However, the window length cannot increase beyond the previously established maximum length using this stale value.
- Therefore, the final maximum length remains correct.

**Important:** In the optimal approach, not every intermediate window is guaranteed to be valid. We only guarantee that the maximum length returned is correct.

---

# 8. Complexity Comparison

| Approach | Time Complexity | Space Complexity |
|---|---|---|
| Brute Force | O(N²) | O(26) |
| Better | O(26N) = O(N) | O(26) |
| Optimal | O(N) | O(26) |

Since the alphabet contains only 26 uppercase letters, the frequency array uses constant space.

---

# 9. Important Code Observations

### 1. Why `hash[26]`?

Because the string contains uppercase English letters from A to Z.

```cpp
int hash[26] = {0};
```

### 2. Character to index conversion

```cpp
hash[s[r] - 'A']++;
```

Example:

```text
'A' - 'A' = 0
'B' - 'A' = 1
'C' - 'A' = 2
```

### 3. Update maximum frequency

```cpp
maxf = max(maxf, hash[s[r] - 'A']);
```

### 4. Calculate replacements

```cpp
int changes = (r - l + 1) - maxf;
```

### 5. Validity condition

```cpp
changes <= k
```

### 6. Invalidity condition

```cpp
changes > k
```

### 7. Shrink the window

Better:

```cpp
while(changes > k) {
    // Shrink
}
```

Optimal:

```cpp
if(changes > k) {
    // Shrink once
}
```

---

# 10. Final Revision Notes

Remember the progression:

**Brute Force**
```text
Generate all substrings
        ↓
Maintain frequency
        ↓
Calculate replacements
        ↓
Update maximum length
```

**Better**
```text
Sliding Window
        ↓
Expand right pointer
        ↓
Update frequency and maxf
        ↓
While window is invalid
        ↓
Shrink and recalculate maxf
        ↓
Update answer
```

**Optimal**
```text
Sliding Window
        ↓
Expand right pointer
        ↓
Update frequency and maxf
        ↓
If window is invalid
        ↓
Shrink only once
        ↓
Do not recalculate maxf
        ↓
Update answer
```

### Key Takeaway

The main learning from this problem is understanding when we can replace `while` with `if` in a Sliding Window problem.

Do not blindly apply this optimization to every sliding window question. It works here because we only need the maximum window length and can maintain that answer using the non-decreasing historical maximum frequency.

---

## 11. Related Problems for Practice

1. [Longest Substring Without Repeating Characters — LC 3](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
2. [Max Consecutive Ones III — LC 1004](https://leetcode.com/problems/max-consecutive-ones-iii/)
3. [Fruit Into Baskets — LC 904](https://leetcode.com/problems/fruit-into-baskets/)
4. [Longest Substring with At Most K Distinct Characters](https://www.geeksforgeeks.org/problems/longest-k-unique-characters-substring0853/1)

**Pattern:** Sliding Window + Two Pointers + Hashing

**Revision Goal:** Understand the transition from Brute Force → Better → Optimal instead of memorizing only the final code.
