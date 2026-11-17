# TASKS.md

## Task 001: FizzBuzz の実装（疎通確認用）

- 状態: 未着手
- ブランチ名: `task/001-fizzbuzz`
- やること:
  - `fizzbuzz.py` を作成し、関数 `fizzbuzz(n: int) -> list[str]` を実装する。1からnまでの各数について、3の倍数は "Fizz"、5の倍数は "Buzz"、両方の倍数は "FizzBuzz"、それ以外はその数の文字列を返す。
  - `tests/test_fizzbuzz.py` に pytest のテストを作成する。少なくとも、3→Fizz、5→Buzz、15→FizzBuzz、通常の数、1〜15の全列、のケースを含めること。
- 完了条件: `python3 -m pytest` が全て通ること。PR本文にテスト実行結果を貼ること。
