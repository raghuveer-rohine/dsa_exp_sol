Determine if a 9 x 9 Sudoku board is valid. Only the filled cells need to be validated according to the following rules:

Each row must contain the digits 1-9 without repetition.
Each column must contain the digits 1-9 without repetition.
Each of the nine 3 x 3 sub-boxes of the grid must contain the digits 1-9 without repetition.
Note:

A Sudoku board (partially filled) could be valid but is not necessarily solvable.
Only the filled cells need to be validated according to the mentioned rules.


```java
   public boolean isValidSudoku(char[][] board) {
        
        Set<String> seen = new HashSet<>();

        for (int r = 0; r < 9; r++) {
            for (int c = 0; c < 9; c++) {

                char num = board[r][c];

                if (num == '.') {
                    continue;
                }

                if (!seen.add(num + " in row " + r) ||
                    !seen.add(num + " in col " + c) ||
                    !seen.add(num + " in box " + (r / 3) + "-" + (c / 3))) {
                    return false;
                }
            }
        }

        return true;
    }
```