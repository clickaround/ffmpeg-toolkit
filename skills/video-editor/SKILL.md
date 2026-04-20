---
name: ffmpeg-video-editor
description: FFmpeg 비디오 편집 — 오디오+비디오+자막 합성, Ken Burns, 수식 오버레이
user-invocable: false
paths: short-video-maker/src/YTB-ffmpeg/VideoEditor.ts
---

## 용도

비디오+오디오+자막을 하나의 영상으로 합성. 정지 이미지에서 비디오 생성(Ken Burns),
수식 PNG 오버레이, FFmpeg preset ultrafast 인코딩.

## 핵심 파일

- `src/YTB-ffmpeg/VideoEditor.ts` — 메인 클래스
- `src/YTB-ffmpeg/SubtitleFilter.ts` — 자막/오버레이 필터 (내부 의존)
- `src/YTB-ffmpeg/utils.ts` — ffmpeg 래퍼, ffprobe, 헬퍼

## 의존성

- **npm**: `fluent-ffmpeg`, `fs-extra`
- **시스템**: ffmpeg 바이너리
- **내부**: `SubtitleFilter`, `ProjectFontConfig`

## API/인터페이스

```typescript
class VideoEditor {
  // 비디오 + 오디오 + 자막 + 씬 오버레이 합성
  async combineVideoWithAudioAndCaptions(
    videoPath: string, audioPath: string, captions: any[],
    outputPath: string, durationSeconds: number,
    orientation: OrientationEnum, config: RenderConfig,
    skipSubtitles?: boolean,
    sceneOverlays?: Array<{ text: string; startMs: number; endMs: number }>,
    fontConfig?: ProjectFontConfig,
    subtitleConfig?: SubtitleConfig
  ): Promise<string>

  // 정지 이미지 → 비디오 (Ken Burns zoom effect)
  async createStaticVideoFromImage(
    imagePath: string, audioPath: string, outputPath: string,
    durationSeconds: number, orientation: OrientationEnum
  ): Promise<string>

  // 이미지 + 수식 PNG 오버레이 → 비디오
  async createStaticVideoWithFormulaOverlay(
    imagePath: string, formulaImagePath: string, audioPath: string,
    outputPath: string, durationSeconds: number, orientation: OrientationEnum
  ): Promise<string>
}
```

## 다른 프로젝트에 이식하기

1. `VideoEditor.ts` + `SubtitleFilter.ts` + `utils.ts` 복사
2. `npm install fluent-ffmpeg fs-extra`
3. ffmpeg 설치 + 폰트 파일 배치

```typescript
import { VideoEditor } from './VideoEditor';
const editor = new VideoEditor();
await editor.combineVideoWithAudioAndCaptions(
  'scene.mp4', 'narration.mp3', captions,
  'output.mp4', 30, 'portrait', { fps: 30 }
);
```

## 주의사항

- 모든 출력에 `-preset ultrafast -crf 23 -pix_fmt yuv420p` 적용 (파일 크기 대폭 감소)
- 필터 8KB 이상 시 자동으로 `-filter_complex_script` 파일 방식 전환 (ENAMETOOLONG 방지)
- Ken Burns: 1.05x zoom + 미세 이동 (subtle, 유튜브 숏츠에 최적)
- sceneOverlays는 상단, 자막은 하단/중앙 (subtitleConfig로 제어)
