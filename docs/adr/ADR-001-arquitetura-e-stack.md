# ADR-001: Adoção de Monolito Modular e Stack Tecnológica .NET / React

## Situação (SIT)
* **Status:** Aceito
* **Data:** 2026-11-15 *(coloca a data atual)*
* **Participantes:** Guilherme da Silva, Nicolas James, Matheus Delapicula, Rodrigo Santos, Igor Lima

## Contexto (CTX)
Precisamos desenvolver o sistema Connexa dentro das limitações de uma equipe pequena (4 a 5 integrantes) e de um semestre letivo, mantendo a manutenibilidade, a separação clara de responsabilidades e evitando a complexidade operacional desnecessária de uma arquitetura de microsserviços distribuídos. Além disso, precisamos garantir fronteiras rígidas entre os domínios de negócio para que o código não se torne um monolito acoplado.

## Decisão (DEC)
Adotamos uma arquitetura de **Monolito Modular** combinada com uma Single Page Application (**SPA em React**). No back-end, utilizamos **.NET (ASP.NET Core Minimal APIs)** organizados em projetos separados por módulo, onde o acesso aos dados é feito via **Entity Framework Core (EF Core)** com **PostgreSQL 17** (um schema isolado por módulo, sem chaves estrangeiras cruzadas). 

## Alternativas Consideradas (ALT)
1. **Microsserviços puros com mensageria (Broker):** Descartado porque a carga esperada e o tamanho da equipe não justificam o alto custo de infraestrutura e complexidade de rede.
2. **Monolito em camadas tradicional:** Descartado porque camadas puras não impedem que código de um domínio acesse diretamente os dados ou regras de outro, gerando forte acoplamento com o tempo.

## Consequências (CON)
* **O que fica mais fácil:** O deploy é unificado e simples (como um monolito), o compilador do C# impede referências circulares entre projetos, e o diretório `Internal` protege as regras internas de cada módulo.
* **O que fica mais difícil:** Exige disciplina da equipe para respeitar as fronteiras modulares e a comunicação via eventos/interfaces públicas (`IUsuarioAtual`).

## Como Verificar (VRF)
* O teste de arquitetura automatizado (`ArquiteturaModularTest` com *NetArchTest*) roda a cada build para garantir que nenhuma regra de dependência entre módulos foi violada[cite: 17, 23].
* O script local `./verificar` e os checks obrigatórios de CI (GitHub Actions) validam a compilação, o linter e todos os testes de aceitação antes de permitir qualquer integração na branch `main`[cite: 16, 28, 30].
