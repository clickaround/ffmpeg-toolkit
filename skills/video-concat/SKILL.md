---
name: ffmpeg-video-concat
description: FFmpeg 비디오 연결 — filter_complex concat + xfade 전환 효과
user-invocable: false
paths: short-video-maker/src/YTB-ffmpeg/VideoConcat.ts
---

## 용도

여러 비디오 클립을 하나로 연결. 단순 concat(fps 정규화 + re-encoding) 또는
xfade 전환 효과(fade, dissolve, wipeleft 등)로 프로페셔널한 씬 전환.

## 핵심 파일

- `src/YTB-ffmpeg/VideoConcat.ts` — 메인 클래스
- `src/YTB-ffmpeg/utils.ts` — `getVideoDuration()`, `runFFmpegSpawn()` 헬퍼

## 의존성

- **npm**: `fluent-ffmpeg`, `fs-extra`
- **시스템**: ffmpeg 바이너리

## API/인터페이스

```typescript
class VideoConcat {
  // filter_complex concat (fps=30 정규화, re-encoding)
  async concatVideos(inputPaths: string[], outputPath: string): Promise<string>

  // xfade 전환 효과 (scale 정규화 + transition)
  async concatVideosWithXfade(
    inputPaths: string[],
    outputPath: string,
    transitionDuration?: number,   // 기본 0.5초
    transitionType?: string,       // 'fade' | 'dissolve' | 'wipeleft' | ...
    targetDimensions?: string      // 기본 '1080x1920' (portrait)
  ): Promise<string>
}

// 지원 transition 타입 (33개):
// fade, wipeleft, wiperight, wipeup, wipedown,
// slideleft, slideright, slideup, slidedown,
// circlecrop, rectcrop, distance, fadeblack, fadewhite,
// radial, smoothleft, smoothright, dissolve, pixelize, ...
```

## 다른 프로젝트에 이식하기

1. `VideoConcat.ts` + `utils.ts` 복사
2. `npm install fluent-ffmpeg fs-extra`
3. ffmpeg 설치

```typescript
import { VideoConcat } from './VideoConcat';
const vc = new VideoConcat();
// 단순 연결
await vc.concatVideos(['clip1.mp4', 'clip2.mp4', 'clip3.mp4'], 'output.mp4');
// fade 전환 (0.5초)
await vc.concatVideosWithXfade(
  ['clip1.mp4', 'clip2.mp4'], 'output.mp4', 0.5, 'fade', '1080x1920'
);
```

## 주의사항

- 단순 concat도 항상 re-encoding 수행 (keyframe/GOP 차이 문제 방지)
- xfade는 모든 클립을 동일 해상도로 scale 후 적용 (VEO 등 다른 해상도 대응)
- `concatVideos`: `-preset ultrafast -crf 23`, 5분 타임아웃
- 잘못된 transition 타입은 자동으로 `'fade'`로 fallback
- 단일 파일 입력 시 복사만 수행
