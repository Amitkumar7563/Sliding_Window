# Sliding Window – Mental Model & Templates

## 1. What is Sliding Window?

Sliding Window ek technique hai jisme hum array ya string ke **continuous (contiguous) subarray/substring** par efficiently kaam karte hain.

Normally, agar hume har possible subarray ka sum ya length calculate karna ho, toh nested loops lag sakte hain → `O(N²)`.

Sliding Window mein hum ek window maintain karte hain using two pointers:

- `left` → window ka starting index.
- `right` → window ka ending index.

Jab window move hoti hai:
- Right se new element add hota hai.
- Left se old element remove hota hai.
- Puri window dobara calculate nahi karni padti.

**Mental Model:**

Right pointer explore karta hai, left pointer unnecessary elements remove karta hai, aur hum current window ki information maintain karte hain.

---

## 2. Window Length Formula

```cpp
windowLength = right - left + 1;
```

Example:

```cpp
left = 2;
right = 5;

// Length = 5 - 2 + 1 = 4
```

Remember: Dono boundaries window ka part hoti hain, isliye `+1` important hai.

---

## 3. Sliding Window ka Core Mental Model

Har problem solve karne se pehle 5 steps samjho:

1. **Expand:** Right pointer move karo.
2. **Update:** New element ko current window state mein add karo.
3. **Validate:** Check karo window valid hai ya nahi.
4. **Shrink:** Invalid hone par left pointer se elements remove karo.
5. **Record:** Answer update karo.

### Window State kya hoti hai?

Problem ke according hume kuch information maintain karni hoti hai.

| Problem | Maintain kya karenge? |
|---|---|
| Maximum sum | Running sum |
| Distinct elements | Frequency map |
| Repeated characters | Character frequency |
| Number of zeros | Zero count |
| Maximum/minimum | Monotonic deque |

**Important:** Window ke andar jo elements present hain, maintained state bhi exactly unhi ko represent karni chahiye.

---

# 4. Types of Sliding Window

Mainly 2 types:

1. Fixed Size Sliding Window
2. Variable Size Sliding Window

---

## Type 1: Fixed Size Sliding Window

Jab question mein fixed size `K` diya ho aur hume exactly K elements ki window process karni ho.

### Common Questions

- Maximum sum of subarray of size K.
- Maximum average subarray.
- First negative number in every window.
- Count distinct elements in every window.
- Maximum/minimum in every window.

### Example

```cpp
arr = [2, 1, 5, 1, 3]
K = 3
```

Windows:

```text
[2, 1, 5] = 8
[1, 5, 1] = 7
[5, 1, 3] = 9
```

Answer = `9`

### C++ Template

```cpp
int left = 0;
int sum = 0;
int maxi = INT_MIN;

for (int right = 0; right < n; right++) {

    // 1. Add incoming element
    sum += arr[right];

    // 2. Shrink if size exceeds K
    if (right - left + 1 > k) {
        sum -= arr[left];
        left++;
    }

    // 3. Process complete window
    if (right - left + 1 == k) {
        maxi = max(maxi, sum);
    }
}
```

### Intuition

- Right element add karega.
- Window size K se badi hui toh left element remove hoga.
- Jab size exactly K ho, answer update karenge.

**Golden Rule:** Fixed window mein size `K` hone par answer process karo.

Time Complexity: `O(N)`

Space Complexity: `O(1)` for running sum.

---

## Type 2: Variable Size Sliding Window

Jab window ka size fixed nahi hota aur condition ke according change hota hai.

Right pointer window expand karta hai. Agar condition invalid ho jaaye, toh left pointer se window shrink karte hain.

### Common Questions

- Longest substring without repeating characters.
- Longest subarray with at most K distinct elements.
- Minimum size subarray sum.
- Minimum Window Substring.
- Maximum consecutive ones after K flips.

### C++ General Template

```cpp
int left = 0;
int ans = 0;

for (int right = 0; right < n; right++) {

    // 1. Add incoming element
    // Update window state

    // 2. Restore validity
    while (/* invalid condition */) {

        // Remove outgoing element
        // Update window state

        left++;
    }

    // 3. Update answer
    ans = max(ans, right - left + 1);
}
```

**Mental Model:**

Expand → Invalid check → Shrink until valid → Update answer.

---

# 5. Longest Valid Window

Jab question mein longest subarray/substring find karna ho jo given condition satisfy karta ho.

### Example

Longest substring without repeating characters.

```cpp
s = "abca"
```

| Step | Window | Status | Max Length |
|---|---|---|---|
| 1 | a | Valid | 1 |
| 2 | ab | Valid | 2 |
| 3 | abc | Valid | 3 |
| 4 | abca | Invalid | - |
| 5 | bca | Valid | 3 |

Answer = `3`

### C++ Template (Frequency Map)

```cpp
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

    ans = max(ans, right - left + 1);
}
```

**Important:** Longest valid window mein pehle window valid banao, uske baad answer update karo.

---

# 6. Smallest Valid Window

Jab question minimum length ki valid subarray/substring maangta hai.

### Mental Model

1. Right se expand karo.
2. Jab window valid ho jaaye, answer update karo.
3. Left se shrink karke aur smaller window find karo.
4. Jab invalid ho jaaye, expansion continue karo.

### C++ Template

```cpp
int left = 0;
int ans = INT_MAX;

for (int right = 0; right < n; right++) {

    // Add incoming element
    // Update window state

    while (/* window is valid */) {

        ans = min(ans, right - left + 1);

        // Remove outgoing element
        // Update window state

        left++;
    }
}

if (ans == INT_MAX) ans = 0;
```

**Remember:** Smallest valid window mein answer update shrinking ke andar hota hai, kyunki har smaller valid window ek candidate hai.

---

# 7. Counting Valid Windows

Kabhi question maximum/minimum length nahi, balki valid subarrays ki total count maangta hai.

Agar current window `[left, right]` valid hai aur condition monotonic hai, toh iske ending index `right` wale valid subarrays ki count:

```cpp
count += right - left + 1;
```

### Example

```cpp
left = 2;
right = 5;
```

Valid windows ending at index 5:

```text
[2...5]
[3...5]
[4...5]
[5...5]
```

Total = `4`

### C++ Template

```cpp
long long count = 0;
int left = 0;

for (int right = 0; right < n; right++) {

    // Add incoming element

    while (/* invalid condition */) {

        // Remove outgoing element
        left++;
    }

    count += right - left + 1;
}
```

**Important:** Ye formula tabhi use karna hai jab current valid window ke har suffix (ending at right) bhi valid ho.

---

# 8. Exactly K vs At Most K

Ek important observation jo counting problems mein bahut useful hai:

```text
Exactly(K) = AtMost(K) - AtMost(K - 1)
```

Example:

Agar hume exactly K distinct elements wale subarrays count karne hain:

```cpp
exactlyK = atMostK(k) - atMostK(k - 1);
```

Ye technique especially useful hai:

- Exactly K distinct integers.
- Exactly K odd numbers.
- Exactly K occurrences of a condition.

---

# 9. When to Use While vs If?

Ye interview aur LeetCode dono ke liye important concept hai.

### Use While

Jab window ko completely valid banana necessary ho.

```cpp
while (invalid) {
    // Shrink window
    left++;
}
```

Use cases:
- Minimum Window Substring.
- Longest valid window.
- Counting valid subarrays.
- At most K distinct elements.

### Use If

Kuch optimized longest-window problems mein single `if` use ho sakta hai.

```cpp
if (invalid) {
    // Remove one element
    left++;
}
```

Is optimization mein window length ko non-decreasing rakha jata hai. Invalid window se maximum answer improve nahi hona chahiye.

**Warning:** Har `while` ko `if` se replace mat karna. Pehle correctness prove karo.

---

# 10. Why Sliding Window is O(N)?

Variable window mein `while` loop dekhkar lag sakta hai ki complexity `O(N²)` hogi.

Lekin:

- Right pointer maximum N times move karta hai.
- Left pointer bhi maximum N times move karta hai.
- Har element window mein ek baar enter aur maximum ek baar exit karta hai.

Total operations proportional to `2N`.

Therefore:

```text
Time Complexity = O(N)
```

Assumption: State update/check average ya worst-case O(1) ho.

---

# 11. When Sliding Window Does NOT Work

Sirf subarray ya substring word dekhkar Sliding Window apply mat karna.

Variable-size Sliding Window ke liye generally condition ka monotonic behaviour important hota hai.

### Example: Negative Numbers

Suppose:

```cpp
arr = [2, -1, 3, -2]
```

Negative numbers ke case mein:

- Window expand karne par sum decrease bhi ho sakta hai.
- Shrink karne par sum increase bhi ho sakta hai.

Isliye sum-based variable window ka normal expand-shrink logic reliable nahi hota.

Alternative approaches:

- Prefix Sum + Hashing
- Binary Search
- Dynamic Programming
- Monotonic Deque
- Fenwick Tree / Segment Tree

**Note:** Fixed-size Sliding Window negative numbers ke saath bhi work kar sakta hai.

---

# 12. How to Recognize Sliding Window?

Question read karte waqt ye observations check karo:

- Kya continuous subarray/substring ki baat ho rahi hai?
- Kya longest, smallest ya count find karna hai?
- Kya left aur right pointers maintain kar sakte hain?
- Kya entering element add aur leaving element remove kar sakte hain?
- Kya window ki validity efficiently check ho sakti hai?
- Kya variable window mein shrinking se validity restore hoti hai?

Agar answers mostly YES hain, toh Sliding Window consider karo.

---

# 13. Common Mistakes

1. Window length formula mein `+1` bhool jaana.
2. Invalid window hone par answer update kar dena.
3. Left move karna but outgoing element state se remove na karna.
4. Frequency map mein zero frequency entries remove na karna, jab map size distinct count represent karta ho.
5. Har problem mein `if` use karna instead of `while`.
6. Smallest window mein answer update karne ki wrong position.
7. Negative numbers par sum-based variable window blindly apply karna.
8. First complete fixed-size window process karna bhool jaana.
9. `O(N²)` assume kar lena because nested while loop hai.
10. Longest, smallest aur counting ke templates ko same samajhna.

---

# 14. Quick Revision Cheat Sheet

| Problem Type | Window Logic | Answer Update |
|---|---|---|
| Fixed Size K | Size > K par shrink | Size == K |
| Longest Valid | Invalid hone tak shrink | Shrink ke baad |
| Smallest Valid | Valid hone tak expand, then shrink | Valid hone par, before shrink |
| Count Valid | Invalid hone tak shrink | `right - left + 1` |
| Exactly K | AtMost K - AtMost K-1 | Difference |

### Final Mental Model

```text
FIXED WINDOW:
Expand → Size check → Shrink → Process

LONGEST WINDOW:
Expand → Invalid? → Shrink → Max update

SMALLEST WINDOW:
Expand → Valid? → Min update → Shrink

COUNT WINDOWS:
Expand → Shrink until valid → Add (right-left+1)

EXACTLY K:
AtMost(K) - AtMost(K-1)
```

---

# 15. My Problem-Solving Checklist

Before coding any Sliding Window problem:

- [ ] Identify fixed or variable window.
- [ ] Define what makes the window valid.
- [ ] Decide what state to maintain.
- [ ] Write the add operation.
- [ ] Write the remove operation.
- [ ] Decide `if` or `while`.
- [ ] Decide where to update the answer.
- [ ] Check edge cases.
- [ ] Verify time and space complexity.

**One line to remember:**

Sliding Window ka main game pointers move karna nahi, balki current window ki state ko correctly maintain karna hai.
