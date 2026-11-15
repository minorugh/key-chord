# key-chord (fork)

Fork of [LemonBreezes/key-chord](https://github.com/LemonBreezes/key-chord).
Map pairs of simultaneously pressed keys (or the same key pressed twice) to commands.

## What this fork changes

`key-chord-mode` hooks into Emacs through `input-method-function`.
`deactivate-input-method` sets that variable to nil unconditionally, so after
toggling an input method (mozc, skk, quail, ...) key-chord is silently detached
while `key-chord-mode` still reports as enabled. Chords then stop working.

This fork re-attaches itself from `input-method-deactivate-hook`:

- `key-chord-mode` adds `key-chord--restore-input-method` to `input-method-deactivate-hook` when enabled, and removes it when disabled
- The function kills the buffer-local `input-method-function` and sets the default value back to `key-chord-input-method`
- `key-chord-mode` now uses `setq-default` instead of `setq`
- Everything else is identical to upstream, and the feature name is still `key-chord`

The old workaround is no longer needed:

```elisp
(defun my-key-chord-ensure ()
  (when key-chord-mode
    (key-chord-mode -1)
    (key-chord-mode 1)))
(add-hook 'input-method-activate-hook   #'my-key-chord-ensure)
(add-hook 'input-method-deactivate-hook #'my-key-chord-ensure)
```

While an input method is active, chords are disabled (the input method owns
`input-method-function`). They come back when the input method is turned off.

## Install

Emacs 29+ with `package-vc`:

```elisp
(leaf key-chord
  :vc (:url "https://github.com/minorugh/key-chord")
  :hook (after-init-hook . key-chord-mode))
```

Or without leaf:

```elisp
(package-vc-install "https://github.com/minorugh/key-chord")
(key-chord-mode 1)
```

If you installed `key-chord` from MELPA, remove it first so the two do not conflict.

## Usage

```elisp
(key-chord-define-global "jk" 'view-mode)
(key-chord-define-global "jj" 'avy-goto-char)
(key-chord-define-global "xx" 'execute-extended-command)
```

Keys must be characters with codes below 256. See `key-chord-two-keys-delay`
and `key-chord-one-key-delay` for timing, and the upstream README for the other
options (`key-chord-typing-detection`, `key-chord-use-key-tracking`, ...).

## License

GPL-2.0-or-later, same as upstream.
Original author: David Andersson. Upstream maintainer: LemonBreezes.

---

# key-chord（fork）日本語

[LemonBreezes/key-chord](https://github.com/LemonBreezes/key-chord) の fork です。

## 変更点

IME（mozc / skk / quail など）を切り替えると、`deactivate-input-method` が
`input-method-function` を無条件に nil にするため、`key-chord-mode` が ON のまま
key-chord だけ外れて chord が効かなくなる問題を修正しています。

- `key-chord-mode` の ON 時に `input-method-deactivate-hook` へ復元関数を登録し、OFF 時に外す
- 復元関数はバッファローカルの `input-method-function` を破棄し、デフォルト値を `key-chord-input-method` に戻す
- `key-chord-mode` の設定を `setq` から `setq-default` に変更
- それ以外は upstream と同じで、feature 名も `key-chord` のまま

従来の `my-key-chord-ensure` と 2 つの `add-hook` は不要になります。
IME が有効な間は chord は効きません。IME を切ると復帰します。

## インストール

```elisp
(leaf key-chord
  :vc (:url "https://github.com/minorugh/key-chord")
  :hook (after-init-hook . key-chord-mode))
```

MELPA 版を入れている場合は、先に削除してください。
