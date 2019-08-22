FROM debian:buster AS builder

RUN apt-get update && apt-get install -y curl xz-utils
COPY ./fetch-extract-ffmpeg .
RUN ./fetch-extract-ffmpeg

# FROM scratch doesn't work if ffmpeg needs to do DNS resolution and HTTPS.
FROM debian:buster-slim
COPY --from=builder /ffmpeg /ffmpeg
