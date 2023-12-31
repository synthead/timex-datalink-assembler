FROM alpine:3.18 as build

RUN apk add wine xvfb
RUN apk add --repository=http://dl-cdn.alpinelinux.org/alpine/edge/testing winetricks

ENV WINEPREFIX=/root/wine

# Install Microsoft Visual C++ 4.
RUN winetricks mfc40

# Download and install Toebes' assembler with a fake framebuffer.
RUN wget --directory-prefix=/root http://www.toebes.com/Datalink/asm6805.zip
RUN unzip /root/asm6805.zip -d /root/asm6805
RUN Xvfb :0 -screen 0 1024x768x16 & DISPLAY=:0 wine /root/asm6805/ASM6805.EXE /s

FROM alpine:3.18 as run

RUN apk add --no-cache wine

ENV WINEPREFIX=/root/wine

COPY --from=build /root/wine /root/wine

# Pretend that the Timex Datalink software is installed.
RUN mkdir -p /root/wine/drive_c/DATALINK/APP /root/wine/drive_c/DATALINK/SND
RUN echo -e "[Main]\r\nPath=C:\\DATALINK\r\n" > /root/wine/drive_c/windows/TIMEXDL.INI

# Work around a bug where VAsm6805.exe attempts to read from the 64-bit Program Files.
RUN ln -s "/root/wine/drive_c/Program Files (x86)/DataLink Devel" "/root/wine/drive_c/Program Files/DataLink Devel"

# Copy a wrapper script to run VAsm6805.exe.
COPY VAsm6805-wrapper /root/VAsm6805-wrapper
