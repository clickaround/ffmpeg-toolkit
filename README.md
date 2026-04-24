# FFmpeg Toolkit for Claude Code

Production-ready FFmpeg automation skills. Battle-tested on 100+ YouTube videos.

## Skills

| Skill | Tier | Usage |
|-------|------|-------|
| `/ffmpeg-toolkit:audio-processor` | Free (MIT) | Audio normalize, trim, concat, extract, convert |
| `/ffmpeg-toolkit:subtitle-filter` | Pro | SRT→ASS, font embedding, drawtext overlays |
| `/ffmpeg-toolkit:video-concat` | Pro | Multi-clip concat, FPS normalize, crossfade |
| `/ffmpeg-toolkit:video-editor` | Pro | Full pipeline: video+audio+subtitle+scenes |

All skills are model-invoked (`user-invocable: false`) — Claude automatically uses them when the task context matches.

## Install

### From marketplace
```
/plugin marketplace add http://168.107.56.106/ffmpeg-toolkit/.claude-plugin/
/plugin install ffmpeg-toolkit@clickaround
```

### Local dev
```
claude --plugin-dir ./plugins/ffmpeg-toolkit
```

## Requirements

- `ffmpeg` binary installed and on PATH
- Node.js 18+ (for `fluent-ffmpeg` wrapper, used by several skills)

## Pro Activation

Pro skills (subtitle-filter, video-concat, video-editor) check for a license file at:
`~/.claude/plugins/ffmpeg-toolkit/license.txt`

Purchase at http://168.107.56.106/ffmpeg-toolkit/ ($49 lifetime or $15/month) — the purchase email contains your license key.

Without license, Pro skills output a purchase prompt instead of running.

## Support

- Free: GitHub issues at github.com/akfldk1028/ffmpeg-toolkit
- Pro: clickaround8@gmail.com

## License

- Free tier skill: MIT (see `LICENSE`)
- Pro tier skills: Proprietary commercial license (see `LICENSE-PRO`)


## Latest posts

- [2026-04-24] New: http://168.107.56.106/clickaround/blog/2026-04-24-ffmpeg-toolkit.html
