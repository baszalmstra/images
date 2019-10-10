# escape=`

# Use the latest Windows Server Core image with .NET Framework 4.7.2.
FROM microsoft/dotnet-framework:4.7.2-sdk-windowsservercore-1803 as base

# Restore the default Windows shell for correct batch processing.
SHELL ["cmd", "/S", "/C"]

# Download channel for fixed install.
ARG CHANNEL_URL=https://aka.ms/vs/15/release/channel
ADD ${CHANNEL_URL} C:\TEMP\VisualStudio.chman

# Download and install Build Tools for Visual Studio 2017 for native desktop workload.
ADD https://aka.ms/vs/15/release/vs_buildtools.exe C:\TEMP\vs_buildtools.exe
RUN C:\TEMP\Install.cmd C:\TEMP\vs_buildtools.exe --quiet --wait --norestart --nocache `
    --channelUri C:\TEMP\VisualStudio.chman `
    --installChannelUri C:\TEMP\VisualStudio.chman `
    --add Microsoft.VisualStudio.Workload.VCTools --includeRecommended`
    --installPath C:\BuildTools

ENTRYPOINT C:\BuildTools\Common7\Tools\VsDevCmd.bat &&
CMD ["powershell.exe", "-NoLogo", "-ExecutionPolicy", "Bypass"]

FROM base as llvm

RUN powershell.exe -ExecutionPolicy RemoteSigned `
    iex (new-object net.webclient).downloadstring('https://get.scoop.sh'); `
    scoop install python git cmake

RUN git clone --single-branch --branch llvmorg-7.1.0 --depth 1 https://github.com/llvm/llvm-project.git && `
    cd llvm-project && mkdir build && cd build && `
    cmake -DLLVM_ENABLE_PROJECTS=clang,lld -G "Visual Studio 15 2017" -A x64 -Thost=x64 ..\llvm && `
    cmake --build . && `
    cmake -DCMAKE_INSTALL_PREFIX=C:\LLVM -P cmake_install.cmake
