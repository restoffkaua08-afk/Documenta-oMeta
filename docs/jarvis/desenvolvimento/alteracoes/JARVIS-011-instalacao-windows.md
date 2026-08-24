# JARVIS-011 — instalação reproduzível do Jarvis Code no Windows

## Estado

Implementado na `main`. A validação local neste ambiente Linux não substitui a
execução do workflow `windows-installer.yml` nem o teste na máquina Windows
final.

## Objetivo

Preparar uma instalação nativa e repetível do Jarvis no Windows, com foco no
agente de terminal, sem obrigar a compilação desktop/Rust durante a instalação
mínima.

## Alterações no instalador

O arquivo `deploy/windows/install.ps1` agora:

- usa por padrão o repositório
  `restoffkaua08-afk/Jarvis-ia`;
- aceita `-Repository` ou `OPENJARVIS_REPO_URL`;
- aceita `-CliOnly` para instalar apenas o núcleo do terminal;
- aceita `-SkipOllama` para configurar modelos posteriormente;
- mantém o modo completo com desktop e extensão Rust;
- valida `jarvis --help` antes de declarar sucesso;
- valida a preparação do benchmark do `jarvis code`;
- preserva instalação opcional do serviço em loopback.

## Desinstalação segura

Foi criado `deploy/windows/uninstall.ps1`.

O desinstalador:

- recusa diretórios amplos como perfil, `LOCALAPPDATA` ou raiz de unidade;
- remove somente o diretório resolvido da instalação;
- remove a entrada Jarvis do PATH do usuário;
- remove o serviço opcional quando existente;
- preserva Python, Git, uv e Ollama;
- permite backup do diretório de dados com `-KeepData`;
- solicita confirmação, exceto quando `-Force` é explícito.

## CI Windows

O workflow `.github/workflows/windows-installer.yml` contém dois jobs:

1. análise sintática de todos os scripts PowerShell;
2. instalação isolada em `windows-latest` com Python 3.13, modo CLI,
   sem Ollama e sem serviço.

O segundo job executa:

- o instalador usando um clone local do checkout;
- o shim `jarvis.cmd --help`;
- `jarvis code --benchmark-prepare`.

## Uso recomendado na máquina final

Instalação apenas do agente de terminal:

```powershell
git clone https://github.com/restoffkaua08-afk/Jarvis-ia.git
cd Jarvis-ia
.\deploy\windows\install.ps1 -CliOnly -SkipService
```

Instalação completa:

```powershell
.\deploy\windows\install.ps1 -SkipService
```

## Commits

- `ccef20bb577757d2192c27243c8d975a7643645a` — adaptação do instalador;
- `8e3e1e66ffd5c2ff89c96cca750ad80970d847b6` — validação Windows em CI;
- `25c9e7e0b6f1ea6770c6ea601d207a2124a3cba2` — desinstalador protegido;
- `0ee80102e5956df81b0ba9f3e8dded9d916b69aa` — documentação operacional.

## Limites e gate de liberação

Ainda não existe um executável monolítico assinado. O produto é instalado por
PowerShell e exposto como `jarvis.cmd`. Transformar isso em um `.exe` não
melhora o raciocínio do agente e acrescenta problemas de assinatura,
SmartScreen, atualizações e dependências nativas.

O instalador não deve ser chamado de versão final até:

- o workflow Windows concluir com sucesso;
- `jarvis code --check` passar na máquina de destino com o modelo escolhido;
- o benchmark JARVIS-010 atingir 100/100 naquela máquina;
- uma tarefa real de projeto ser executada e revisada.
