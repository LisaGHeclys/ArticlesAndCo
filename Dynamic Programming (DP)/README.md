# Dynamic Programming (DP)
*by Lisa Mai Linh Glaziou*

*created: May 19th 2024*

*updated: May 19th 2024*

As I was more inclined to work on side projects, I found myself leetcoding more and more. I also found new ways to approach problems which is Dynamic Programming, also known as DP.
So let’s dive in, and learn what is Dynamic Programming and how I used them to solve some problems.

---

# What is Dynamic Programming ?

**Dynamic Programming (DP)** is a method used in mathematics and computer science to solve complex problems by breaking them down into simpler subproblems. By solving each subproblem only once and storing the results, it avoids redundant computations, leading to more efficient solutions for a wide range of problems.

# How Does Dynamic Programming (DP) Work?

- **Identify Subproblems:** Divide the main problem into smaller, independent subproblems.
- **Store Solutions:** Solve each subproblem and store the solution in a table or array.
- **Build Up Solutions:** Use the stored solutions to build up the solution to the main problem.
- **Avoid Redundancy:** By storing solutions, DP ensures that each subproblem is solved only once, reducing computation time.

---

# Solved Use Cases from LeetCode

This is the breakdown of 3 problems I resolve using DP, of course, you can solve the same problems with many other ways (recursive, dfs etc…).

## Fibonnaci Number

“The Fibonacci numbers, commonly denoted F(n) form a sequence, called the Fibonacci sequence, such that each number is the sum of the two preceding ones, starting from 0 and 1. That is,

F(0) = 0, F(1) = 1
F(n) = F(n - 1) + F(n - 2), for n > 1.
Given n, calculate F(n).”

```tsx
function fib(n: number): number {
    if (n <= 1) return n;
    
    const dp: number[] = new Array(n).fill(1);
    
    for (let i = 2; i < n; i ++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }
    return dp[n - 1]
};
```

We are creating a table to store the result of F(n - 1) + F(n - 2) for F(n), considering that we are already skipping the known result of F(0) and F(1). 

## Climbing Stairs

“You are climbing a staircase. It takes `n` steps to reach the top.

Each time you can either climb `1` or `2` steps. 

In how many distinct ways can you climb to the top ?”

```tsx
function climbStairs(n: number): number {
    if (n <= 1) return 1;
    
    const dp: number[] = new Array(n + 1).fill(1);

    for (let i = 2; i <= n; i ++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }
    return dp[n]
};
```

Same logic as the fibonnaci one.

## Regular Expression Matching

“Given an input string `s` and a pattern `p`, implement regular expression matching with support for `'.'` and `'*'` where:

- `'.'` Matches any single character.
- `'*'` Matches zero or more of the preceding element.

The matching should cover the **entire** input string (not partial).”

```tsx
function isMatch(s: string, p: string): boolean {
    const dp: boolean[][] = Array(s.length + 1).fill(null).map(() => Array(p.length + 1).fill(false));
    dp[0][0] = true;

    for (let j = 1; j <= p.length; j++) {
        if (p[j - 1] === '*') {
            dp[0][j] = dp[0][j - 2];
        }
    }

    for (let i = 1; i <= s.length; i++) {
        for (let j = 1; j <= p.length; j++) {
            if (p[j - 1] === s[i - 1] || p[j - 1] === '.') {
                dp[i][j] = dp[i - 1][j - 1];
            } else if (p[j - 1] === '*') {
                dp[i][j] = dp[i][j - 2];
                if (p[j - 2] === s[i - 1] || p[j - 2] === '.') {
                    dp[i][j] = dp[i][j] || dp[i - 1][j];
                }
            }
        }
    }

    return dp[s.length][p.length];
};
```

1. **State Definition**:
    - **`dp[i][j]`** will be **`true`** if the first **`i`** characters of string **`s`** match the first **`j`** characters of pattern **`p`**.
2. **State Transition**:
    - If the current characters match (**`s[i-1] == p[j-1]`** or **`p[j-1] == '.'`**), then **`dp[i][j] = dp[i-1][j-1]`**.
    - If the current pattern character is **`'*'`**, we check two cases:
        - Zero occurrences of the preceding character (**`dp[i][j] = dp[i][j-2]`**).
        - One or more occurrences of the preceding character (**`dp[i][j] = dp[i-1][j]`** if **`s[i-1] == p[j-2]`** or **`p[j-2] == '.'`**).
3. **Initialization**:
    - **`dp[0][0] = true`**: An empty string matches an empty pattern.
    - For patterns like **`a*`**, **`a*b*`**, etc., initialize the first row considering zero occurrences of the characters.
4. **Final Result**:
    - The result will be stored in **`dp[s.length][p.length]`**, representing whether the entire string matches the entire pattern.

# Resources, Case of Studies and Examples

GeeksForGeeks Community (May 2, 2024). [Dynamic Programming or DP](https://www.geeksforgeeks.org/dynamic-programming/)