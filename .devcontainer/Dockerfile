# devcontainer用のベースイメージ
FROM mcr.microsoft.com/vscode/devcontainers/base:ubuntu

# ls系のエイリアスを予め使えるようにコメントを外す
RUN sed -i 's/^#alias ll=/alias ll=/; s/^#alias la=/alias la=/; s/^#alias l=/alias l=/' /home/vscode/.bashrc

# node_moduleディレクトリの権限を調整
RUN mkdir -p /workspace/front/node_modules && \
    chown -R vscode:vscode /workspace/front/node_modules
