---
title: "フォームパーツのコンポーネント実装"
free: false
---

# フォームパーツをコンポーネント化する

これから編集画面を実装しますが、編集画面では多くの入力フォームが登場します。`input` や `textarea` などの入力フォームはWebアプリ内のいたるところで登場し、多くの場合同じフォームデザインが採用されます。これらは共有コンポーネントにして使いまわせるようにしましょう。

## tailwindcss-formsのセットアップ

フォームパーツにはブラウザ標準のスタイルが当たっておりTailwind CSSと干渉するため、Tailwind CSSの公式プラグインである [tailwindcss-forms](https://github.com/tailwindlabs/tailwindcss-forms) を導入します。これによりフォーム標準のスタイルがリセットされ、Tailwind CSSから直感的にデザインできるようになります。

```bash:ターミナル
npm install @tailwindcss/forms
```

```js:tailwind.config.js
...
plugins: [
  // 追加
  require('@tailwindcss/forms'),
],
```

# フィールドグループを作成する

入力フィールドは一般的に以下の構成で成り立ちます。

![](https://storage.googleapis.com/zenn-user-upload/46f37ff84432-20211130.png)

このうち、入力フォーム以外は共通であるkとが多いのでフィールドグループとして共有コンポーネントにします。

```tsx:components/field-group.tsx
import { ReactNode } from 'react';

type Props = {
  id: string; // id属性に使用
  label: string; // ラベル
  children: ReactNode; // 入力フィールドが入ります
  error?: string; // エラーテキスト
  currentlength?: number; // 現在の文字数
  action?: ReactNode; // アクションをつける場合
  maxLength?: number; // 最大文字数
  required?: boolean; // 必須入力か否か
};

const FieldGroup = ({
  error,
  label,
  currentlength,
  action,
  required,
  maxLength,
  children,
  id,
}: Props) => {
  return (
    <div>
      <div>
        <label htmlFor={id}>
          {label}
          {required && <sup className="text-red-500">*</sup>}
        </label>
      </div>
      <div className="flex space-x-2">
        {children}
        {action}
      </div>
      <div className="flex text-sm space-x-4">
        {error && <p className="text-red-500 flex-1">{error}</p>}
        {maxLength && (
          <p className="text-gray-500 ml-auto">
            {currentlength || 0} / {maxLength}
          </p>
        )}
      </div>
    </div>
  );
};

export default FieldGroup;
```

上記はシンプルなReactコンポーネントなので詳細は割愛します。

# Inputコンポーネントを作成する

上記のフィールドグループを使ってInputコンポーネントを作成します。

```tsx:components/input-field.tsx
import { InputHTMLAttributes, ReactNode } from 'react';
import { UseFormRegisterReturn } from 'react-hook-form';
import { classNames } from '../lib/class-names';
import FieldGroup from './field-group';

type Props = {
  label: string;
  error?: string;
  currentlength?: number;
  action?: ReactNode;
  register: UseFormRegisterReturn;
} & InputHTMLAttributes<HTMLInputElement>;

const InputField = ({
  error,
  label,
  currentlength,
  register,
  action,
  className,
  ...props
}: Props) => {
  return (
    <FieldGroup
      label={label}
      error={error}
      currentlength={currentlength}
      action={action}
      maxLength={props.maxLength}
      required={props.required}
      id={register.name}
    >
      <input
        id={register.name}
        className={classNames(
          'flex-1 border rounded px-2 py-1 w-full',
          className
        )}
        {...register}
        {...props}
      />
    </FieldGroup>
  );
};

export default InputField;
```

`register` は React Hook Form の機能です。これによりフォームとReact Hook Formに接続することができます。

`InputHTMLAttributes<HTMLInputElement>` の部分は実際に使いたい入力フォームに応じて変更します。

- `InputHTMLAttributes` - 属性の型。今回は `input` タグに該当する属性の型を指定している。
- `<HTMLInputElement>` - onChangeなどのイベントで渡される要素の型。今回はinputタグなのでこの指定にしている。通常は上記タグ属性の型と同じタグの型を指定します。

`&` で型をつなぐことで、二つの型の内容をマージ（融合）しています。

# Textareaコンポーネントを作成する

Textareaは入力項目に応じて動的に高さを増やしたいので、[react-textarea-autosize](https://github.com/Andarist/react-textarea-autosize)を使います。入力の行数に応じて自動的にフォームを拡げてくれます。

![](https://storage.googleapis.com/zenn-user-upload/058f71b1cd59-20211130.gif)
*入力内容に応じて入力欄が拡がる様子*

```bash:ターミナル
npm install react-textarea-autosize
```

```tsx:components/textarea-field.tsx
import { ReactNode } from 'react';
import { UseFormRegisterReturn } from 'react-hook-form';
import TextareaAutosize, {
  TextareaAutosizeProps,
} from 'react-textarea-autosize';
import { classNames } from '../lib/class-names';
import FieldGroup from './field-group';

type Props = {
  label: string;
  error?: string;
  currentlength?: number;
  action?: ReactNode;
  register: UseFormRegisterReturn;
} & TextareaAutosizeProps;

const TextareaField = ({
  error,
  label,
  currentlength,
  register,
  action,
  className,
  ...props
}: Props) => {
  return (
    <FieldGroup
      label={label}
      error={error}
      currentlength={currentlength}
      action={action}
      maxLength={props.maxLength}
      required={props.required}
      id={register.name}
    >
      <TextareaAutosize
        id={register.name}
        className={classNames(
          'flex-1 border rounded px-2 py-1 w-full',
          className
        )}
        {...register}
        {...props}
      />
    </FieldGroup>
  );
};

export default TextareaField;
```

今回はライブラリが用意する `TextareaAutosize` にPropsを渡すので型指定が `TextareaAutosizeProps` になっている点に注意しましょう。

以上で基本的なフォームコンポーネントの実装は完了です。今後はこれらのフォームコンポーネントを使って編集画面や設定画面を実装していきます。