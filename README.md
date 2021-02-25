# await-dispatch-actions

## やりたかったこと

`workflow_dispatch` を呼び出して、その実行した Workflow の Run が終了するのを待つだけの Actions がほしかった。

## できないことがわかった

結論としては既存の API の組み合わせではできない。

- `workflow_dispatch` を呼び出す [Create a workflow dispatch event](https://docs.github.com/en/rest/reference/actions#create-a-workflow-dispatch-event) API では実行した Run の ID は返却されないため自身が呼び出した Run を断定することができない
  - 呼び出す前に Workflow の Run の最終IDを記録して Dispatch 実行後に新たに生まれた Run が呼び出した Run であるという「推測」はできる
  - これが「推測」でしかない理由として例えば人為的に短期間に2回連続で呼び出したときにどちらが優先されるかは保証されないからである
  - ただすでに「推測」により実行した Run がどれかを判定している Actions も存在する
    - [dysnix/dispatch-and-wait](https://github.com/dysnix/dispatch-and-wait)
  - ただ先述した理由によりこの実装はあまりよくなさそうに思える
- ならば `inputs` にジョブIDなどを入力すればいいのかと思ったが `workflow_dispatch` の入力値を取れる API が存在しなかった
  - [List workflow runs](https://docs.github.com/en/rest/reference/actions#list-workflow-runs) で Workflow に属する Runs の一覧が取れるが返却値に `inputs` の値が取れる内容は存在しなかった
    - サンプルに無いだけかな？と思ったけど実際のAPIのレスポンスを見ても同様
  - Why?

## 考えうる対応策

実際にやってはいない。

- Artifacts は取れるので、そこにジョブの特定情報を吐き出すか
- Step の名前に `inputs` が使えるのでそこに吐き出すか
