---
background: /ales-nesetril-ex_p4AaBxbs-unsplash.jpg
highlighter: shiki
lineNumbers: true
colorSchema: 'dark'
drawings:
  persist: false
fonts:
  sans: 'Noto Sans JP'
  serif: 'Noto Sans JP'
  mono: 'Inconsolata'
---

<div class="opacity-50 text-md">JAWS-UG CDK支部 #1 〜初回拡大版〜</div>

# CDKを活用して年間700万円以上のコストを削減した話

JAWS-UG CDK支部 コアメンバー  
吉川 幸弘  
2022/06/01

<div class="pt-4">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Press Space for next page <carbon:arrow-right class="inline"/>
  </span>
</div>

<div class="abs-br m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
  <a href="https://github.com/slidevjs/slidev" target="_blank" alt="GitHub"
    class="text-xl icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---
layout: intro
---

# 自己紹介

<div class="flex opacity-90">
  <div class="basis-1/4">
    <div  class="rounded-full h-40 w-40 mx-auto" style="background-color: #6CBDCD">
      <img src="/icon_bird.png" class="rounded-full" />
    </div>
    <div class="py-3 text-center">
      <div class="text-4xl font-bold">吉川 幸弘</div>
      <div class="pt-1 text-2xl opacity-80">@WinterYukky</div>
    </div>
    <div class="flex justify-around px-5">
      <a href="https://github.com/WinterYukky" target="_blank" alt="GitHub" class="text-xl icon-btn opacity-50 !border-none !hover:text-white">
        <mdi-github class="text-3xl" />
      </a>
      <a href="https://twitter.com/WinterYukky" target="_blank" alt="Twitter" class="text-xl icon-btn opacity-50 !border-none !hover:text-sky-500">
        <mdi-twitter class="text-3xl text-sky-500" />
      </a>
    </div>
  </div>
  <div class="basis-3/4 pl-10">
    <ul class="text-2xl">
      <li>通称「ゆっきー」</li>
      <li>大阪のスタートアップ所属エンジニア</li>
      <li>普段のお仕事
        <ul class="text-xl">
          <li>お客様のAWS導入サポート</li>
          <li>プロダクト企画・開発・営業</li>
        </ul>
      </li>
      <li>好きなテクノロジー
        <ul class="text-xl">
          <li>静的型付け言語</li>
          <li>Infrastructure as Code (特にAWS CDK💖)</li>
        </ul>
      </li>
    </ul>
  </div>
</div>

---

# 免責事項

<br><br><br><br><br>
<div class="text-center">

## ❌ AWS CDKを使うからコストが削減できる

<br>

## ⭕ コスト削減の実現にAWS CDKを使った

</div>

---

# アジェンダ

<br><br><br><br>

1. 削減可能なコストを特定する方法
1. 実際にあった問題
1. 改善策の提案
1. 改善結果
1. さいごに

<style>
li {
  font-size: 1.5rem !important;
}
</style>

---

# 削減可能なコストを特定する方法


<div v-click class="grid grid-cols-2 h-4/5 items-center">
  <div class="text-2xl text-center">
    <strong class="text-orange-400">Trusted Advisor</strong> を確認しましょう
  </div>
  <div class="grid justify-center">
    <img src="/AWS-Trusted-Advisor.svg" width="200" height="200" />
    <div class="text-center pt-2">AWS Trusted Advisor</div>
  </div>
</div>

---

# Trusted Advisor で発見できる削減可能なコスト

<div class="grid grid-cols-2">
  <ul class="text-sm">
    <li>Amazon EC2 リザーブドインスタンスの最適化</li>
    <li :class="{ 'text-orange-400': $slidev.nav.clicks === 1 }">低使用率の Amazon EC2 インスタンス</li>
    <li>アイドル状態のロードバランサー</li>
    <li>使用率の低い Amazon EBS ボリューム</li>
    <li>関連付けられていない Elastic IP アドレス</li>
    <li>Amazon RDS のアイドル状態の DB インスタンス</li>
    <li>Amazon Route 53 レイテンシーリソースレコードセット</li>
    <li>Amazon EC2 Reserved Instance Lease Expiration</li>
    <li>使用率の低い Amazon Redshift クラスター</li>
    <li>Savings Plan</li>
    <li>Amazon ElastiCache のリザーブドノードの最適化</li>
    <li>Amazon Redshift のリザーブドノードの最適化</li>
    <li>Amazon RDS のリザーブドインスタンスの最適化</li>
    <li>Amazon Opensearch Service のリザーブドインスタンスの最適化</li>
    <li>エラー率の高い AWS Lambda 関数</li>
    <li>過度にタイムアウトした AWS Lambda 関数</li>
  </ul>
  <div class="h-4/5 flex justify-center items-center text-5xl text-orange-300">現在16種類</div>
</div>

<div v-click class="text-orange-400">
  <arrow x1="470" y1="180" x2="330" y2="140" />
  <div class="absolute top-45 left-120 text-2xl">今回はこの項目について</div>
</div>

---

# 低使用率の Amazon EC2 インスタンス

<div class="grid grid-cols-2 h-4/5 items-center">
  <div>
    <h2 class="pb-4 text-orange-400">考えられる主な原因</h2>
    <ul>
      <li v-click>リホストしてAWSへ移行した直後</li>
      <li v-click>過剰なインスタンスサイズ</li>
      <li v-click>スケーリングの閾値が適切ではない</li>
      <li v-click :class="{ 'text-orange-400': $slidev.nav.clicks === 5 }">必要ないのに常設している</li>
    </ul>
  </div>
  <div class="flex justify-center">
    <img src="/Amazon-EC2_R4-Instance_dark-bg.svg" />
  </div>
</div>

<div v-click class="text-orange-400">
  <arrow x1="400" y1="450" x2="300" y2="380" />
  <div class="absolute bottom-20 left-105 text-2xl">改善したのはここ</div>
</div>

---

# 実際にあったケース
複数システムからのデータ中継地点としていたWindowsインスタンス

<div class="grid grid-cols-5 h-4/5 items-center">
  <div class="grid gap-3">
    <div class="p-3 rounded bg-white text-center">
      <hulft></hulft>
    </div>
    <div class="p-3 rounded bg-white text-center">
      <hulft></hulft>
    </div>
    <div class="p-3 rounded bg-white text-center">
      <hulft></hulft>
    </div>
  </div>
  <div class="justify-self-center self-center grid opacity-80">
    <div class="justify-self-center">
      <mdi-archive class="text-2xl"/>
      <mdi-archive class="text-2xl"/>
      <mdi-archive class="text-2xl"/>
    </div>
    <mdi-arrow-right class="text-5xl justify-self-center"/>
    <div class="text-xs">圧縮アーカイブ形式のCSV</div>
  </div>
  <div class="justify-self-center self-center grid justify-center justify-items-center gap-3">
    <div class="p-3 rounded bg-white text-center">
      <hulft tag="div"></hulft>
    </div>
    <img src="/Amazon-EC2_R4-Instance_dark-bg.svg"/>
    <div class="text-orange-400 font-bold underline">問題のインスタンス</div>
    <div class="text-xs opacity-80">CSVの展開担当</div>
  </div>
  <div class="justify-self-center self-center grid opacity-80">
    <div class="justify-self-center">
      <mdi-file-delimited class="text-2xl"/>
      <mdi-file-delimited class="text-2xl"/>
      <mdi-file-delimited class="text-2xl"/>
    </div>
    <div class="justify-self-center">
      <mdi-file-delimited class="text-2xl"/>
      <mdi-file-delimited class="text-2xl"/>
      <mdi-file-delimited class="text-2xl"/>
    </div>
    <div class="justify-self-center">
      <mdi-file-delimited class="text-2xl"/>
      <mdi-file-delimited class="text-2xl"/>
      <mdi-file-delimited class="text-2xl"/>
    </div>
    <mdi-arrow-right class="text-5xl justify-self-center"/>
    <div class="text-xs text-center">展開されたCSV</div>
  </div>
  <div class="justify-self-center self-center grid justify-center justify-items-center gap-3">
    <div><mdi-database class="text-7xl text-yellow-400"/></div>
    DWH
  </div>
</div>

---

# かかっていたコスト

<div class="grid grid-cols-2 items-center h-4/5 divide-x">
  <ul class="justify-self-center">
    <li v-click>インスタンス
      <ul>
        <li>25万円/月</li>
      </ul>
    </li>
    <li v-click>監視サービス契約
      <ul>
        <li>35万円/月</li>
      </ul>
    </li>
    <li v-click>約60万円/月</li>
  </ul>
  <div v-click class="h-full grid justify-center items-center">
    <div class="text-center">
      <div>朝と昼に一回動くだけで</div>
      <div class="text-3xl">年間720万💰</div>
    </div>
  </div>
</div>

---

# 以前からEC2インスタンスへは不満あり

<br><br><br>

- 望まないEC2インスタンスのメンテナンス
  - Windows Server 2012 R2 ⇒ Windows Server 2019
  - OSアップグレード後に若干の事故
- 24時間365日監視サービスを契約
  - CloudWatchで代用可能なサービス内容
  - IAMユーザーの認証情報を渡していてセキュリティ的にもよくない
  - そもそも、利用用途的にコスパが悪い

---

# 提案したのが AWS Step Functions × CDK

<div class="grid grid-cols-5 h-4/5 items-center">
  <div class="col-span-2 justify-self-center">
    <img src="/AWS-Step-Function_light-bg.svg" width="200" height="200" />
    <div class="text-center pt-2">AWS Step Functions</div>
  </div>
  <div class="text-center">
    <mdi-close class="text-7xl" />
  </div>
  <div class="col-span-2 justify-self-center">
    <img src="/AWS-CDK.svg" width="200" height="200" />
    <div class="text-center pt-2">AWS CDK</div>
  </div>
</div>

---

# AWS Step Functionsとは

<div class="grid grid-cols-2 items-center h-4/5">
  <div class="justify-self-center">
    <ul>
      <li>サーバーレスオーケストレーションサービス</li>
      <li>障害、並列化、サービス統合、可観測性などを管理</li>
      <li>デベロッパーはビジネスロジックに集中できる</li>
    </ul>
  </div>
  <div class="grid justify-center">
    <img src="/AWS-Step-Function_light-bg.svg" width="200" height="200" />
    <div class="text-center pt-2">AWS Step Functions</div>
  </div>
</div>

---
layout: center
---

# 個人的に感じるAWS Step Functionsの辛み

---

# Workflow Studioで構築する場合の辛み

<div class="grid grid-cols-2 gap-3 h-4/5">
  <div class="flex items-center">
    <ul>
      <li class="mb-5">👍フローが視覚的に把握できる</li>
      <li v-click>🤔利用するサービスの画面と行ったり来たり</li>
      <li v-click>🤔利用サービスが増える度、管理が大変に</li>
      <li v-click>🤔サービス含めた複製を作るのが困難・・・</li>
    </ul>
  </div>
  <div class="self-center">
    <img src="/StepFunctionsWorkStudio.png"/>
  </div>
</div>

---

# CloudFormationで書く場合の辛み

<div class="grid grid-cols-2 gap-3">
  <div class="h-4/5 flex items-center">
    <ul>
      <li>👍CloudFormation内で完結できる</li>
      <li class="mb-5">👍スタック単位で複製も可能</li>
      <li v-click>🤔ASLで記述するためフローの把握がしにくい</li>
      <li v-click>🤔記述が多いため難読化しがち</li>
      <li v-click>🤔さっきのフロー構築にも300行以上必要・・・</li>
    </ul>
  </div>
  <div>
  
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: An example template for a Step Functions.
Resources:
  MyStateMachine:
    Type: AWS::StepFunctions::StateMachine
      Properties: 
        Definition: 
          Comment: "A description of my state machine"
          StartAt: "DecompressTar"
          States: 
            DecompressTar: 
              Type: "Task"
              Resource: "arn:aws:states:::lambda:invoke"
              OutputPath: "$.Payload"
              Parameters: 
                Payload.$: "$"
              Retry: 
              -
                ErrorEquals: 
                - "Lambda.ServiceException"
                - "Lambda.AWSLambdaException"
                - "Lambda.SdkClientException"
                IntervalSeconds: "2" # 実際はまだ300行以上ある
```
  
  </div>
</div>

---
layout: center
---

<div class="rounded-lg bg-sky-500 p-6">
  <div class="text-3xl">Step Functions のメンテナンス辛いッピ・・・（泣）</div>
</div>

<img src="/icon_bird.png" class="absolute bottom-0 right-0 h-50" />
<mdi-water class="text-sky-400 absolute bottom-22 right-25 text-3xl" />

---
layout: center
---

# そこで CDK の出番

<img src="/icon_bird.png" class="absolute bottom-0 right-0 h-50" />
<mdi-lightbulb-on class="text-yellow-400 absolute bottom-45 right-25 text-7xl" />

---

# CDK だとフローをイメージしやすい

<div class="grid grid-cols-7 h-4/5">
  <div class="col-span-3 my-6 flex items-center justify-center bg-gray-200 rounded h-100">
    <img src="/StepFunctionsWorkStudioFromCDKOutput.svg" class="h-full" >
  </div>
  <mdi-arrow-right class="text-4xl self-center justify-self-center text-orange-400" :class="{ 'opacity-20': $slidev.nav.clicks !== 0 }"/>
  <div class="col-span-3 self-center">
    <div class="opacity-60 text-xs">↓ 左図をCDKでコーディングした例</div>
  
```ts {all|1-8|3|4|5-8|6|7|10-11|all}
// タスクの詳細とフローを分離することで
// フローの理解に集中できる
tarDecompressTask
  .addCatch(errorNoticeTask)
  .next(new Map(this, 'AllFilesProcess')
    .iterator(fileProcessTask)
    .addCatch(errorNoticeTask)
  );

// フローの記述は任意の単位で分割も可能
errorNoticeTask.next(new Fail(this, 'ErrorEnd'));
```
  
  <ul class="pt-6" :class="{ 'opacity-20': $slidev.nav.clicks !== 0 }">
      <li>👍個々のタスクとフローの定義が分割可能</li>
      <li>👍チェインメソッドで流れが分かる</li>
    </ul>
  </div>
</div>

<arrow v-if="$slidev.nav.clicks===2" x1="470" y1="180" x2="330" y2="185" width="5" class="text-orange-500"/>
<arrow v-if="$slidev.nav.clicks===3" x1="470" y1="230" x2="330" y2="285" width="5" class="text-orange-500"/>
<arrow v-if="$slidev.nav.clicks===4" x1="470" y1="250" x2="290" y2="285" width="5" class="text-orange-500"/>
<arrow v-if="$slidev.nav.clicks===5" x1="460" y1="255" x2="270" y2="295" width="5" class="text-orange-500"/>
<arrow v-if="$slidev.nav.clicks===6" x1="400" y1="300" x2="270" y2="370" width="5" class="text-orange-500"/>
<arrow v-if="$slidev.nav.clicks===7" x1="450" y1="350" x2="310" y2="425" width="5" class="text-orange-500"/>


---

# CDK だとLambdaの事前ビルドが不要

<div class="grid grid-cols-2 gap-x-4 h-4/5 mt-16">
  <ul class="self-center pb-25">
    <li>👍L2.5コンストラクトの機能</li>
    <li>👍Synth時に自動的にビルド</li>
    <li>👍Nodejs は esbuild or Docker が利用可能</li>
  </ul>
  <div class="grid items-between">
    <div>
      <div class="opacity-60 text-xs">NodejsFunctionコンストラクトの例</div>
  
```ts
const fileProcess = new LambdaInvoke(this, 'FileProcessTask', {
  lambdaFunction: new NodejsFunction(this, 'FileProcess')
})
```

  </div>
  <div>
    <div class="opacity-60 text-xs">TypeScriptで書いたLambdaの例</div>

```ts
// lib/example-stack.FileProcess.ts
interface ProcessFile {
  bucket: string
  key: string
  destination: string
}

export const handler = async (file: ProcessFile) => {
  console.log(file.key);
}
```

  </div>
  </div>
</div>

---

# CDK だと細かいIAM Roleの設定が不要

<div class="grid grid-cols-2 gap-x-4 h-4/5">
  <ul class="self-center">
    <li>👍Roleの設定忘れを回避</li>
    <li>👍ワークフローの記述に集中可能</li>
    <li>👍記述が減って可読性アップ</li>
  </ul>
  <div class="grid items-between">
    <div>
      <div class="opacity-60 text-xs">出力されたポリシーの定義</div>
  
```json
{
  "Type": "AWS::IAM::Policy",
  "Properties": {
    "PolicyDocument": {
      "Statement": [
        {
          "Action": "lambda:InvokeFunction",
          "Effect": "Allow",
          "Resource": [ /** フロー中のLambda関数達 */]
        },
        {
          "Action": "sns:Publish",
          "Effect": "Allow",
          "Resource": {
            "Ref": "StepFunctionErrorNoticeTopicA2CE0566"
          }
        }
      ]
    }
  }
}
```

  </div>
  </div>
</div>

---

# CDK だとメンテナンスしやすい

<div class="grid grid-cols-7 gap-x-4 h-4/5">
  <ul class="col-span-3 self-center">
    <li>👍これだけで前例のフローが構築可能</li>
    <li>👍エディタのサジェストでサクサク書ける</li>
    <li>👍TypeCheckで凡ミスを防げる</li>
  </ul>
  <div class="col-span-4 self-center">
  
```ts
export class ExampleStack extends Stack {
  constructor(scope: Construct, id: string, props?: StackProps) {
    super(scope, id, props);

    const tarDecompress = new LambdaInvoke(this, "TarDecompressTask", {
      lambdaFunction: new NodejsFunction(this, 'TarDecompress'),
      outputPath: '$.Payload'
    })
    const fileProcess = new LambdaInvoke(this, 'FileProcessTask', {
      lambdaFunction: new NodejsFunction(this, 'FileProcess')
    })
    const errorNotice = new SnsPublish(this, 'ErrorNotice', {
      topic: new Topic(this, 'StepFunctionErrorNoticeTopic'),
      message: TaskInput.fromJsonPathAt('$')
    })
    new StateMachine(this, 'MyStateMachine', {
      definition: tarDecompress.addCatch(errorNotice)
        .next(new Map(this, 'AllFilesProcess')
          .iterator(fileProcess)
          .addCatch(errorNotice.next(new Fail(this, 'ErrorEnd')))
        )
    });
  }
}
```

  </div>
</div>

---

# 改善後の流れ
Windowsインスタンスを排除し、S3のイベントでStep Functionsを起動するように

<div class="grid grid-cols-7 h-4/5 items-center">
  <div class="grid gap-3">
    <div class="text-xs p-1 rounded bg-white text-orange-400 text-center">クラウドストレージオプション</div>
    <div class="text-center"><mdi-plus></mdi-plus></div>
    <div class="p-3 rounded bg-white text-center">
      <hulft></hulft>
    </div>
    <div class="p-3 rounded bg-white text-center">
      <hulft></hulft>
    </div>
    <div class="p-3 rounded bg-white text-center">
      <hulft></hulft>
    </div>
  </div>
  <div class="justify-self-center self-center grid opacity-80">
    <div class="justify-self-center">
      <mdi-archive class="text-2xl"/>
      <mdi-archive class="text-2xl"/>
      <mdi-archive class="text-2xl"/>
    </div>
    <mdi-arrow-right class="text-5xl justify-self-center"/>
    <div class="text-xs text-center">アーカイブ</div>
  </div>
  <div class="justify-self-center self-center grid justify-center justify-items-center gap-3">
    <img src="/Amazon-Simple-Storage-Service-S3.svg"/>
    <div class="text-xs opacity-80">S3</div>
  </div>
  <div class="justify-self-center self-center grid opacity-80">
    <mdi-code-json class="text-2xl justify-self-center"/>
    <mdi-arrow-right class="text-5xl justify-self-center"/>
    <div class="text-xs text-center">イベント</div>
  </div>
  <div class="justify-self-center self-center grid justify-center justify-items-center gap-3">
    <img src="/AWS-Step-Function_light-bg.svg"/>
    <div class="text-xs opacity-80">Step Functions</div>
  </div>
  <div class="justify-self-center self-center grid opacity-80">
    <div class="justify-self-center">
      <mdi-file-delimited class="text-2xl"/>
      <mdi-file-delimited class="text-2xl"/>
      <mdi-file-delimited class="text-2xl"/>
    </div>
    <div class="justify-self-center">
      <mdi-file-delimited class="text-2xl"/>
      <mdi-file-delimited class="text-2xl"/>
      <mdi-file-delimited class="text-2xl"/>
    </div>
    <div class="justify-self-center">
      <mdi-file-delimited class="text-2xl"/>
      <mdi-file-delimited class="text-2xl"/>
      <mdi-file-delimited class="text-2xl"/>
    </div>
    <mdi-arrow-right class="text-5xl justify-self-center"/>
    <div class="text-xs text-center">展開されたCSV</div>
  </div>
  <div class="justify-self-center self-center grid justify-center justify-items-center gap-3">
    <div><mdi-database class="text-7xl text-yellow-400"/></div>
    DWH
  </div>
</div>

---

# 最終的に得られたもの

<br><br>
<ul>
  <li v-click>ランニングコスト
    <ul>
      <li>Windows インスタンスにかかっていたコスト
        <ul>
          <li>およそ700万円/年</li>
        </ul>
      </li>
    </ul>
  </li>
  <li v-click class="mt-5">メンテナンスコスト
    <ul>
      <li>Windows インスタンスにかかっていたメンテナンスコスト
        <ul>
          <li>???万円/年</li>
        </ul>
      </li>
      <li>CDK以外で作成した場合の、Step Functionsのメンテナンスコスト
        <ul>
          <li>???万円/年</li>
        </ul>
      </li>
    </ul>
  </li>
</ul>

---

# さいごに
CDKを活用して年間700万円<strong class="text-orange-300 px-1">以上</strong>のコストを削減した話

<br><br><br><br><br>
<div class="text-center text-3xl">
  <div>額面はおよそ700万円</div>
  <div>実際はそれ<strong class="text-orange-400 text-4xl px-1">以上</strong>に削減できたのでは？</div>
</div>

---
layout: center
---

# Thanks!

<img src="/icon_bird.png" class="absolute bottom-0 right-0 h-30" />