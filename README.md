# key-chord-20240910.1441

The latest version does not work, so use the older version in your own repository.
(20250430)

## 修正内容 (20250508)

旧版（20240910.1441）の `key-chord-input-method` 内、`sit-for` による待機処理を
`read-event` タイムアウト方式に変更した。

```elisp
;; 変更前
(sit-for delay nil)

;; 変更後
(let ((next (read-event nil nil delay)))
  (when next
    (push next unread-command-events))
  (null next))
```

**変更理由：**
`sit-for` はキー入力を待つあいだ再描画・タイマー処理を実行するため、
重いバッファや負荷時に処理が詰まり「失速」が発生していた。
`read-event` にタイムアウトを渡す方式（key-seq と同じ実装）では
描画処理を一切走らせないため、この問題が解消される。

タイムアウト時に `read-event` が返した non-nil イベントは
`unread-command-events` に差し戻すことで入力を取りこぼさない。

変更箇所はこの1か所のみで、他のコードは原版のまま。
