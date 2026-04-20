---
name: ffmpeg-subtitle-filter
description: FFmpeg 자막 필터 — TikTok 스타일 word-by-word + 제목 오버레이 + 다국어 폰트
user-invocable: false
paths: short-video-maker/src/YTB-ffmpeg/SubtitleFilter.ts
---

## 용도

TikTok/Shorts 스타일 word-by-word 하이라이트 자막, 씬별 제목 오버레이, 다국어 폰트 자동 선택.
뉴스/교육/엔터테인먼트 영상의 자막+제목을 FFmpeg drawtext 필터로 생성.

## 핵심 파일

- `src/YTB-ffmpeg/SubtitleFilter.ts` — 메인 클래스
- `src/YTB-ffmpeg/utils.ts` — `findFontByLanguage()`, `resolveFontPreset()`, FFmpeg 헬퍼

## 의존성

- **npm**: `fluent-ffmpeg`, `fs-extra`
- **시스템**: ffmpeg 바이너리
- **폰트**: `font/` 디렉토리에 BlackHanSans, NotoSansCJK, NotoSansDevanagari 등

## API/인터페이스

```typescript
interface ProjectFontConfig {
  title_font?: FontPreset;
  subtitle_font?: FontPreset;
  subtitle_size?: number;
  title_size?: number;
  title_color?: string;       // 예: 'white'
  title_bg_color?: string;    // 'none' = 배경 없음 (뉴스 스타일)
  subtitle_color?: string;
  language?: string;           // 'ko' | 'en' | 'hi' 등
}

class SubtitleFilter {
  // TikTok 스타일 word-by-word 자막 (2-3줄 자동 줄넘김)
  createSubtitleFilter(captions, orientation, tempDir, subtitleConfig?): { filter: string; textFilePaths: string[] }

  // 씬별 제목 오버레이 (상단)
  createSceneOverlayFilter(overlays, orientation, tempDir, fontConfig?): { filter: string; textFilePaths: string[] }

  // 30+ 캡션일 때 간소화 자막 (yPosition 지원)
  createSimplifiedSubtitleFilter(captions, orientation, tempDir, config?): { filter: string; textFilePaths: string[] }
}
```

## 다른 프로젝트에 이식하기

1. `SubtitleFilter.ts` + `utils.ts` 복사
2. `font/` 디렉토리에 필요한 폰트 파일 배치
3. `npm install fluent-ffmpeg fs-extra`
4. ffmpeg 바이너리 설치 (`apt install ffmpeg` 또는 static build)

```typescript
import { SubtitleFilter } from './SubtitleFilter';
const sf = new SubtitleFilter();
const result = sf.createSubtitleFilter(captions, 'portrait', '/tmp', { yPosition: 'h*0.88' });
// result.filter → FFmpeg complexFilter에 추가
```

## 주의사항

- Windows: 경로 백슬래시 → 슬래시 자동 변환, 콜론 이스케이프 처리됨
- drawtext에서 textfile 방식 사용 (명령줄 길이 초과 방지)
- 캡션 230+개 시 `createSimplifiedSubtitleFilter` 자동 전환
- `title_bg_color='none'` 설정 시 배경 박스 없이 텍스트만 표시
