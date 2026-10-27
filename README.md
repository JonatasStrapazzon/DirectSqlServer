# Direct SQL Server

Instalador do **Direct SQL Server**: aplicativo Windows que restaura um `.bak` (ou um ZIP/RAR/7z contendo backup) em uma nova base no SQL Server local, com autenticação do Windows.

## Instalar

1. Baixe `DirectSqlServer-win-Setup.exe` na [versão mais recente](../../releases/latest).
2. Execute. A instalação é por usuário (não pede administrador) e instala o .NET 9 Desktop Runtime se ele ainda não existir.
3. Abra **Direct SQL Server** pelo menu Iniciar, selecione ou arraste um backup e informe o nome da nova base.

> O instalador não é assinado digitalmente. Se o Windows mostrar "O Windows protegeu o computador", clique em **Mais informações → Executar assim mesmo**.

Requer SQL Server local na instância padrão (`MSSQLSERVER`) e permissão para restaurar bancos.

## Atualizações

O app verifica novas versões ao abrir. Quando houver uma, o link do rodapé muda para **Nova versão X disponível: atualizar agora**. Também dá para verificar manualmente em **Verificar atualizações**.

Preferências, tema e logs ficam em `%APPDATA%\DirectSqlServer` e `%LOCALAPPDATA%\DirectSqlServer` e não são apagados na atualização.
