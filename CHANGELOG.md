# Notas de versão

## 1.7.0

### Scripts SQL depois de restaurar
- Novo botão **Executar scripts** e um lápis para **editar** os comandos. Eles rodam na base indicada em "Nome da nova base", todos em uma única transação (se um falhar, nada é alterado), com `GO` para separar lotes.
- Nova opção **Executar os scripts SQL salvos na base restaurada**, desmarcada por padrão e lembrada após reiniciar o PC: roda os scripts automaticamente quando a restauração termina com sucesso. Se eles falharem, a base continua restaurada e só os scripts são desfeitos.
- Vem com o script padrão (ambiente de homologação e e-mails de teste). Os scripts editados ficam salvos em `%APPDATA%\DirectSqlServer\scripts.sql`, mesmo após reiniciar o PC ou atualizar o app.

## 1.6.0

### Instalador e atualização automática
- **Instalador por usuário** (`DirectSqlServer-win-Setup.exe`), sem administrador. Cria atalhos na área de trabalho e no menu Iniciar e instala o .NET 9 Desktop Runtime se faltar.
- **Atualização automática** pelos Releases do GitHub: o app verifica ao abrir e avisa no rodapé quando há versão nova. Também há o link **Verificar atualizações**.
- Desinstalar o app remove o menu do Explorer; os atalhos passam a ser gerenciados pelo instalador.

### Por baixo dos panos
- O app migrou de .NET Framework 4.8 para **.NET 9**; a conexão ao SQL Server usa `Microsoft.Data.SqlClient` (mesma conexão local por memória compartilhada e autenticação do Windows).
- ZIPs com nomes de arquivo inválidos no Windows (criados em outros sistemas) agora são listados em vez de recusados.
- Funcionalidades de restauração, compactados, preferências e tema inalteradas.

## 1.3.1

- Visual claro renovado e botão de tema claro/escuro. Última versão distribuída como ZIP (.NET Framework 4.8).
