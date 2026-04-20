---
name: ffmpeg-audio-processor
description: FFmpeg 오디오 처리 — PCM→MP3, concat, crossfade, silence, normalize
user-invocable: false
paths: short-video-maker/src/YTB-ffmpeg/AudioProcessor.ts
---

## 용도

TTS 출력(PCM/MP3/WAV)을 정규화, 변환, 이어붙이기. Gemini TTS의 raw PCM(24kHz 16bit mono)을
MP3로 변환하고 씬간 crossfade로 자연스럽게 연결.

## 핵심 파일

- `src/YTB-ffmpeg/AudioProcessor.ts` — 메인 클래스
- `src/YTB-ffmpeg/utils.ts` — ffmpeg 래퍼

## 의존성

- **npm**: `fluent-ffmpeg`
- **시스템**: ffmpeg 바이너리

## API/인터페이스

```typescript
class AudioProcessor {
  // PCM(24kHz mono) → MP3 변환 (0.1초 무음 패딩 자동 추가)
  async savePcmToMp3(audio: ArrayBuffer, filePath: string): Promise<string>

  // 오디오 파일 이어붙이기 (단순 concat)
  async concatAudios(inputPaths: string[], outputPath: string): Promise<string>

  // crossfade로 자연스럽게 이어붙이기 (기본 0.08초)
  async concatAudiosWithCrossfade(
    inputPaths: string[], outputPath: string, crossfadeDuration?: number
  ): Promise<string>

  // 무음 오디오 생성 (skipTTS 모드용)
  async generateSilentAudio(outputPath: string, duration: number): Promise<string>

  // Whisper용 정규화 (16kHz mono WAV) — PCM 자동 감지
  async saveNormalizedAudio(audio: ArrayBuffer, outputPath: string): Promise<string>

  // ffprobe로 오디오 길이 측정
  async getAudioDuration(filePath: string): Promise<number>

  // 오디오 트리밍
  async trimAudio(inputPath: string, outputPath: string, duration: number): Promise<string>

  // 버퍼 → MP3 파일 저장
  async saveToMp3(audio: ArrayBuffer, filePath: string): Promise<string>
}
```

## 다른 프로젝트에 이식하기

1. `AudioProcessor.ts` + `utils.ts` 복사
2. `npm install fluent-ffmpeg`
3. ffmpeg 설치

```typescript
import { AudioProcessor } from './AudioProcessor';
const ap = new AudioProcessor();
// Gemini TTS PCM → MP3
await ap.savePcmToMp3(pcmBuffer, '/tmp/scene1.mp3');
// 씬별 MP3 crossfade 연결
await ap.concatAudiosWithCrossfade(['/tmp/s1.mp3', '/tmp/s2.mp3'], '/tmp/full.mp3', 0.1);
```

## 주의사항

- `savePcmToMp3`: PCM 끝에 0.1초 무음 자동 추가 (씬간 자연스러운 쉼)
- `saveNormalizedAudio`: 헤더 4바이트로 MP3/WAV/OGG 자동 감지, 없으면 PCM 처리
- crossfade 0.08~0.1초가 숏츠에 최적 (0.05=답답, 0.5=늘어짐)
- 단일 파일 input 시 복사만 수행 (불필요한 re-encoding 방지)
