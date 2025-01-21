---
title: React 19の新しいフック機能を探る
tags:
  - React
private: false
updated_at: '2025-01-22T01:33:00+09:00'
id: 
organization_url_name: null
slide: false
ignorePublish: false
---

こんにちは！今回はReact 19で新しく追加・改善されたフックについて詳しく見ていきましょう。

## 目次
1. [useActionState](#useactionstate)
2. [useOptimistic](#useoptimistic)
3. [useTransition](#usetransition)
4. [useFormStatus](#useformstatus)
5. [use](#use)

## useActionState

`useActionState`はReact 19で新しく導入されたフックで、サーバーアクションの状態を管理するために使用されます。

### 定義

サーバーアクションの実行状態と結果を追跡・管理できるフックです。

### React 19以前のコード

以前は、サーバーアクションの状態を手動で管理する必要がありました：
```jsx
function TodoForm() {
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState(null);

  async function handleSubmit(formData) {
    setIsLoading(true);
    try {
      await addTodo(formData);
    } catch (err) {
      setError(err);
    } finally {
      setIsLoading(false);
    }
  }

  return (
    <form action={handleSubmit}>
      {isLoading && <p>読み込み中...</p>}
      {error && <p>エラーが発生しました：{error.message}</p>}
      {/* フォームの内容 */}
    </form>
  );
}
```

### React 19でのコード

```jsx
function TodoForm() {
  const [addTodoState, addTodo] = useActionState(async (formData) => {
    'use server';
    // サーバーアクションのロジック
  });

  return (
    <form action={addTodo}>
      {addTodoState.pending && <p>読み込み中...</p>}
      {addTodoState.error && <p>エラーが発生しました：{addTodoState.error.message}</p>}
      {/* フォームの内容 */}
    </form>
  );
}
```

## useOptimistic

`useOptimistic`は、ユーザーインターフェースを即座に更新しながら、バックグラウンドで実際の更新を処理する楽観的UIパターンを実装するために使用されます。

### 定義

現在の状態と楽観的更新のための関数を受け取り、即座にUIを更新するフックです。

### React 19以前のコード

```jsx
function TodoList() {
  const [todos, setTodos] = useState([]);
  
  async function handleComplete(todoId) {
    // UIがサーバーレスポンスを待つ必要がある
    await completeTodo(todoId);
    const updatedTodos = await fetchTodos();
    setTodos(updatedTodos);
  }

  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>
          <button onClick={() => handleComplete(todo.id)}>
            {todo.completed ? '完了' : '未完了'}
          </button>
        </li>
      ))}
    </ul>
  );
}
```

### React 19でのコード

```jsx
function TodoList() {
  const [todos, setTodos] = useState([]);
  const [optimisticTodos, addOptimisticTodo] = useOptimistic(
    todos,
    (state, newTodo) => {
      return state.map(todo =>
        todo.id === newTodo.id 
          ? { ...todo, completed: !todo.completed }
          : todo
      );
    }
  );

  async function handleComplete(todoId) {
    // UIが即座に更新される
    addOptimisticTodo({ id: todoId, completed: true });
    await completeTodo(todoId);
  }

  return (
    <ul>
      {optimisticTodos.map(todo => (
        <li key={todo.id}>
          <button onClick={() => handleComplete(todo.id)}>
            {todo.completed ? '完了' : '未完了'}
          </button>
        </li>
      ))}
    </ul>
  );
}
```

## useTransition

`useTransition`はReact 18で導入され、React 19でさらに改善されました。

### 定義
UI更新の優先順位を指定してユーザー体験を向上させるフックです。

### React 19でのコード
- サーバーコンポーネントとの統合強化
- 自動エラーバウンダリー処理
- パフォーマンスの最適化

```jsx
function SearchResults() {
  const [isPending, startTransition] = useTransition();
  const [searchQuery, setSearchQuery] = useState('');

  function handleSearch(e) {
    startTransition(() => {
      setSearchQuery(e.target.value);
    });
  }

  return (
    <div>
      <input onChange={handleSearch} />
      {isPending ? (
        <p>検索中...</p>
      ) : (
        <ResultsList query={searchQuery} />
      )}
    </div>
  );
}
```

## useFormStatus

`useFormStatus`はフォームの送信状態を追跡する新しいフックです。

### 定義

フォームの送信状態（pending、submittingなど）を追跡・管理します。

### React 19以前のコード

```jsx
function SubmitButton() {
  const [isSubmitting, setIsSubmitting] = useState(false);

  async function handleSubmit(e) {
    setIsSubmitting(true);
    try {
      await submitForm(e);
    } finally {
      setIsSubmitting(false);
    }
  }

  return (
    <button disabled={isSubmitting}>
      {isSubmitting ? '送信中...' : '送信'}
    </button>
  );
}
```

### React 19でのコード

```jsx
function SubmitButton() {
  const { pending } = useFormStatus();

  return (
    <button disabled={pending}>
      {pending ? '送信中...' : '送信'}
    </button>
  );
}
```

## use

`use`フックは、PromiseやContextをより柔軟に使用できるようにする新しいフックです。

### 定義

PromiseやContextをコンポーネント内のどこでも使用できるようにするフックです。

### React 19以前のコード

```jsx
function UserProfile({ userPromise }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    userPromise.then(data => setUser(data));
  }, [userPromise]);

  if (!user) return <p>読み込み中...</p>;

  return <h1>こんにちは、{user.name}さん！</h1>;
}
```

### React 19でのコード

```jsx
function UserProfile({ userPromise }) {
  const user = use(userPromise);
  return <h1>こんにちは、{user.name}さん！</h1>;
}
```

## 結論

React 19は、サーバーコンポーネントとの統合、フォーム処理、状態管理など、様々な領域で開発者体験を大きく改善しました。特に`useActionState`や`useFormStatus`などの新しいフックは、サーバー状態の管理をより簡単にし、`useOptimistic`はより良いユーザー体験を提供するのに役立ちます。

これらの変更により、Reactアプリケーションの開発がより効率的で直感的になり、特にサーバーコンポーネントに関連する機能が大幅に強化されたことは注目に値します。