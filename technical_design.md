# Objective

You are given an array of integers representing the prices of a single stock on various days (each index in the array represents a different day). You are also given an integer k, which represents the number of transactions you are allowed to make. One transaction consists of buying the stock on a given day and selling it on another, later day. Write a function that returns the maximum profit that you can make buying and selling the stock, given k transactions. Note that you can only hold 1 share of the stock at a time; in other words, you cannot buy more than 1 share of the stock on any given day and you cannot buy a share of the stock if you are still holding another share. Note that you also don't need to use all k transactions that you're allowed.

Sample input: [5, 11, 3, 50, 60, 90], 2
<br/>Sample output: 93 (Buy: 5, Sell: 11; Buy: 3, Sell: 90)

# Strategy 1 - Iterate the blocked prices, and iterate the session

Given this
```javascript
const prices = [5, 11, 3, 50, 60, 90]
const numberOfSessions = 2
```

Step 1 - Set what is walkable
```javascript
const blocked = [5, 11]
const walkable = [3, 50, 60, 90]
```

Step 2 - Set the buying price
```javascript
const blocked = [5, 11]
const purchase = 3 // Third price
const walkable = [50, 60, 90] // Must be subsequent
```

Step 3 - Set the selling price and record the gain/loss

It's okay if we lose money because the other sessiosn could gain to compensate. Also, it's possible that a stock is not profitable.

```javascript
const blocked = [5, 11]
const purchase = 3 // Third price
const sale = 50 // The index is after "purchase", so this is a valid sale.

const gain = sale - purchase // 47
```

Step 4 - Repeat: Record another sale price. Keep doing this until we hit the end of the array.

```javascript
const blocked = [5, 11]
const purchase = 3 // Third price
const sale = 60 // The index is after "purchase", so this is a valid sale.

const gain = sale - purchase // 57
```

Step 5 - Move the blocked dates

```javascript
const blocked = [5, 11, 3]
// All the stock prices are [5, 11, 3, 50, 60, 90]
```

The number of blocked prices can be as high or as low as possible as long as:
* There is minimum 2 remaining prices for the walking.
* The number of blocked prices is >= number of other sessions * 2. EG: Given 3 sessions, there are 2 blocked sessions, therefore 4 array items are blocked.

Step 5 Alternate - When walking a session that is in between session(s)

```javascript
const prices = [5, 11, 3, 50, 60, 90, 91, 92, 93, 94]

const sessions = 3
const sessionCurrent = 2

// Minimum: number of sessions on the left (or right) * 2
const blockedLeft = [5, 11]
const blockedRight = [93, 94]
```

# Strategy 2 - Same as Strategy 1, but simplify the constraints

Forget about avoiding 100% of all the constraints. Only avoid the constraints that are absolutely impossible.

For example, given
```javascript
const prices = [5, 11, 3, 50, 60, 90, 91, 92, 93, 94]
const sessionsCount = 3
```

When finding all the possible prices for Session 2, we know that the first two and last two prices are absolutely impossible because Sessions 1 and 3 need at least 2 prices each.

**The big takeaway** - BUT, don't worry finding all the possible constraints that are caused by Sessions 1 and 3.

Therefore, the general order of operations are:
1. For each session, find all the possible price pairs. Use only constraints that are certainly impossible in any scenario.
2. Find the best combination of all the sessions, BUT avoid combinations where multiple sessions overlap.

**In other words**, the iteration of possible constraints in Strategy 1 is, in this strategy, delayed until Step 2 above.