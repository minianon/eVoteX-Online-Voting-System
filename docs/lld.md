# eVoteX Online Voting System: Low-Level Design

For the big picture see the [High-Level Design](hld.md).

## 1. Data

```c
struct Date { unsigned int day, month, year; };

typedef struct voter {
    char uname[50];
    int id;
    char password[50];
    int area;
    int has_voted;      // 0 or 1
} voting;               // up to MAX_VOTERS (100)

typedef struct candidate {
    char fname[25], lname[25];
    char vote_sign[2];  // party sign
    int vote_count;
} candid;               // up to MAX_CANDIDATES (5)
```

## 2. Menu

| Choice | Function |
|---|---|
| 1 | `insert_candid` |
| 2 | `register_voter` (only when logged out) |
| 3 | `login_logout` |
| 4 | `update_profile` (logged in) |
| 5 | `display_voting_date` |
| 6 | `display_candidates` (shows the logged-in voter's profile and voted status) |
| 7 | `vote` (logged in, not yet voted) |
| 8 | `display_results` |
| 9 | Quit |

## 3. Session states

```mermaid
stateDiagram-v2
  [*] --> LoggedOut: admin password ok (log = 0)
  LoggedOut --> LoggedIn: login with voter ID + password (log = 2)
  LoggedIn --> LoggedOut: choose Login/Logout again
  LoggedIn --> Voted: vote by party sign (has_voted = 1)
  Voted --> LoggedOut: logout
```

## 4. Vote

```mermaid
flowchart TB
  A[vote] --> B{logged in?}
  B -->|no| X[ask to log in]
  B -->|yes| C{has_voted == 1?}
  C -->|yes| Y[already voted]
  C -->|no| D[enter party sign]
  D --> E{sign matches a candidate?}
  E -->|no| Z[party not found]
  E -->|yes| F[vote_count++, has_voted = 1]
```

## 5. Notes

Voter passwords are kept in plain text in memory, the code opens `Password.txt` while the file in the repo is `password.txt` (fails on case-sensitive systems like Linux), and `system("cls")` is Windows-only.
