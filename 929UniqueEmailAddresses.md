# 進め方

Step1 : 問題を解く。

Step2 : 他の人のPRを参照し、コメントする。

Step3 : 3回続けてエラーが出ないように書く。ドキュメントを参照する。

# 実践

## Step1

正規表現がオートマトンで実装されるみたいな、どこかで読んだことがある気がする

```python
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        # 一文字ずつ見ていけばいけそう
        # 計算量は100 * 100 くらい。大丈夫そう。
        # これは何が見たい問題だろう
        # setに保存していくか。
        edited_emails = set() 
        for email in emails:
            edited = list()
            index = 0

            # 前半
            while True:
                if index >= len(email):
                    raise ValueError('@が含まれていません')
                char = email[index]
                if char == '.':
                    index += 1
                    continue
                if char == '+':
                    while email[index] != '@':
                        index += 1
                        if index >= len(email):
                            raise ValueError('@が含まれていません')
                    break
                if char == '@':
                    break
                edited.append(char)
                index += 1

            edited.append(email[index:])
            print(edited)
            edited_emails.add(tuple(edited))
        return len(edited_emails)
            

                

        
```

```python
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        edited_emails = set()
        
        for email in emails:
            edited = list()
            index = 0

            # 前半
            while True:
                if index >= len(email):
                    raise ValueError('@が含まれていません')
                char = email[index]
                if char == '.':
                    index += 1
                    continue
                if char == '+':
                    index = email.find('@', index)
                    break
                if char == '@':
                    break
                edited.append(char)
                index += 1

            edited.append(email[index:])
            edited_emails.add(''.join(edited))

        return len(edited_emails)
```

## Step2

### 同じ問題を解いた人のプルリクを見る

- エラーを考える。どんなケースで使われるかを想定する

→メールアドレスをグループ分けするものだと仮定すると、変なものが混じってても例外を発生させないほうがよさそう。

→登録処理のようなものだとすると、例外を発生させるべき。

- 関数化してみやすく
- メールアドレスがどんなものか調べたくなるべき

→ローカル部の”@”は許容される。ドメイン部には使えないのでrsplitを使えば回避できる

```python
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        
        def canonicalize_email(email):
            local, domain = email.rsplit('@', 1)
            local = local.split("+")[0]
            local = local.replace('.', '')
            return '@'.join([local,domain])
        
        canonicalized = set()
        for email in emails:
            print(canonicalize_email(email))
            canonicalized.add(canonicalize_email(email))
        return len(canonicalized)
```

## Step3

### 3回連続で再現できるようになる
```python
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        
        def canonicalize_email(email):
            local, domain = email.rsplit('@', 1)
            local = local.split('+')[0]
            local = local.replace('.', '')
            return '@'.join((local, domain))
        
        unique_emails = set()
        for email in emails:
            unique_emails.add(canonicalize_email(email))
        return len(unique_emails)
```

