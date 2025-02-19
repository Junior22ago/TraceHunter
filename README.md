#!/bin/bash 

# Exibe o nome do script
echo "\033[0;34m TraceHunter-Forensic Collector \033[0m"

# Verifica se o script está sendo executado como root
if [[ $EUID -ne 0 ]]; then
   echo -e "\033[0;31m Este script precisa ser executado como root. \033[0m"
   exit 1
fi 

# Define o diretório onde os arquivos coletados serão armazenados
COLLECTED_DIR="collected_files"
mkdir -p "$COLLECTED_DIR"  # Cria o diretório de coleta, caso não exista

# Exibe mensagem indicando o início da coleta de informações
echo "\033[1;35mColetando arquivos do sistema... \033[0m"

# Coletando informações sobre discos e partições
echo "\033[0;95mListando informações sobre discos e partições...\033[0m"
lsblk > "$COLLECTED_DIR/disk_info.txt"  # Salva a lista de discos e partições

# Coletando informações de conexões de rede
echo "\033[0;95mColetando informações de rede...\033[0m"
ss > "$COLLECTED_DIR/active_connections.txt"  # Salva conexões ativas
netstat > "$COLLECTED_DIR/open_ports.txt"  # Salva as portas abertas

# Coletando lista de processos
echo "\033[0;95m Coletando lista de processos...\033[0m"
ps > "$COLLECTED_DIR/process_list.txt"  # Salva lista de processos em execução

# Coletando logs do sistema
echo "\033[0;95m Coletando logs do sistema...\033[0m"
cp /var/log/syslog "$COLLECTED_DIR/syslog.log"
cp /var/log/auth.log "$COLLECTED_DIR/auth.log"
cp /var/log/dmesg "$COLLECTED_DIR/dmesg.log"

# Coletando arquivos de configuração
echo "\033[0;95m Coletando arquivos de configuração...\033[0m"
cp -r /etc "$COLLECTED_DIR/etc"  # Faz backup do diretório /etc

# Listando o diretório raiz
echo "\033[0;95m Listando o diretório raiz...\033[0m"
ls -la / > "$COLLECTED_DIR/root_dir_list.txt"  # Lista arquivos no diretório raiz

# Compactando os arquivos coletados em um arquivo tar.gz
echo "\033[0;95m Compactando arquivos coletados...\033[0m"
tar -czvf "TraceHunter_$(hostname)_$(date +'%Y%m%d_%H%M%S').tar.gz" -C "$COLLECTED_DIR"
