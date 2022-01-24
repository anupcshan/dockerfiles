FROM debian:bullseye AS builder

RUN apt-get update && apt-get install -y curl xz-utils
COPY ./fetch-extract-ffmpeg .
RUN ./fetch-extract-ffmpeg

# FROM scratch doesn't work if ffmpeg needs to do DNS resolution and HTTPS.
FROM debian:bullseye-slim
COPY --from=builder /ffmpeg /ffmpeg
