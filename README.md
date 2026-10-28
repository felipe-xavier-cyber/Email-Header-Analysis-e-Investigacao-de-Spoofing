# 🔍 Email Header Analysis — Spoofing vs Account Takeover

![Foco](https://img.shields.io/badge/foco-Email%20Security-blue)
![DFIR](https://img.shields.io/badge/foco-DFIR-purple)
![M365](https://img.shields.io/badge/Microsoft%20365-0078D4)
![SPF](https://img.shields.io/badge/SPF-DKIM-DMARC-informational)

> Estudo de caso real: um e-mail suspeito pedia a **alteração de dados bancários**. A pergunta a responder era técnica e objetiva — o e-mail era **falso (spoofing externo)** ou a conta do remetente havia sido **comprometida (Account Takeover)**?

## 🎯 O desafio

E-mails de fraude financeira costumam ser tratados como "phishing" genérico. A diferença entre **spoofing** e **ATO** muda completamente a resposta:

| Cenário | O que significa | Resposta |
| :--- | :--- | :--- |
| **Spoofing** | Alguém forja o remetente; a conta real está intacta | Bloquear domínio/IP, ajustar DMARC, orientar o usuário |
| **ATO** | A conta real está nas mãos do atacante | Contenção imediata, revogação de sessão, reset de credenciais |

Errar o diagnóstico significa ou bloquear um remetente legítimo, ou deixar um atacante com acesso válido à empresa.

## 🔬 Metodologia

1. **Extração dos cabeçalhos completos** da mensagem no Microsoft 365.
2. **Validação dos mecanismos de autenticação** de e-mail (SPF, DKIM, DMARC).
3. **Análise dos metadados de roteamento** para identificar o tenant e a infraestrutura de origem.
4. **Correlação** entre autenticidade técnica da origem e o comportamento da mensagem.

## 📊 Resultados da autenticação

| Mecanismo | Resultado | Leitura |
| :--- | :--- | :--- |
| **SPF** | `pass` | O IP de envio está autorizado pelo domínio remetente |
| **DKIM** | `pass` | A mensagem foi assinada criptograficamente pelo domínio e não foi alterada |
| **DMARC** | `none` | Sem política DMARC aplicável — não há alinhamento forçado, mas também não há falha |

> **Ponto-chave:** SPF e DKIM em `pass` provam que a mensagem **saiu de dentro da infraestrutura legítima**. Um atacante externo teria dificuldade de produzir essa combinação.

## 🧾 Metadados de roteamento analisados

| Campo | O que revela |
| :--- | :--- |
| `X-MS-Exchange-CrossTenant-id` | Identifica o tenant do Microsoft 365 que originou a mensagem |
| `Message-ID` | Identifica o servidor que gerou a mensagem (ex.: `BN6PR11MB3921`) |
| Servidores de origem (`BN6PR11MB3921`) | Hostname no padrão de servidores **Exchange Online** |

A infraestrutura observada corresponde ao **tenant Microsoft 365 legítimo da empresa** — não a um servidor de terceiros tentando se passar pelo domínio.

## ⚖️ Veredito

**Spoofing externo: descartado.**
**Account Takeover: forte indício.**

Se a origem é autêntica e a mensagem pede alteração de dados bancários, então **quem escreveu tinha acesso legítimo à conta** — o que caracteriza conta comprometida, não domínio forjado.

O detalhe do `DMARC: none` merece atenção: ele **não** invalida SPF/DKIM `pass`, mas mostra que o domínio não tinha política de enforcement. É exatamente esse vácuo que permite que mensagens maliciosas vindas de contas comprometidas passem sem atrito.

## 🧩 Mapeamento MITRE ATT&CK

| Técnica | ID | Relação com o caso |
| :--- | :--- | :--- |
| Phishing | `T1566` | Vetor inicial (e-mail com pedido de alteração bancária) |
| Valid Accounts | `T1078` | Uso de credenciais legítimas da conta comprometida |
| Email Collection | `T1114` | Acesso à caixa de correio para reconhecimento e fraude |

## 🛡️ Recomendações de hardening

- **Publicar DMARC com política crescente**: `p=none` → `p=quarantine` → `p=reject`.
- **Adotar MFA resistente a phishing** (FIDO2/passkeys) para contas com acesso a dados financeiros.
- **Conditional Access** com restrição de país/dispositivo para contas sensíveis.
- **Monitorar regras de encaminhamento** criadas fora do padrão — indicador clássico de ATO.
- **Treinar usuários** para desconfiar de mudanças de dados bancários, mesmo com remetente interno.
- **Verificação fora de banda**: confirmar alterações bancárias por telefone/Teams, nunca pelo próprio e-mail.

## 📚 Referências

- [RFC 7208 — SPF](https://datatracker.ietf.org/doc/html/rfc7208)
- [RFC 6376 — DKIM](https://datatracker.ietf.org/doc/html/rfc6376)
- [RFC 7489 — DMARC](https://datatracker.ietf.org/doc/html/rfc7489)
- [Microsoft 365 — Anti-phishing policies](https://learn.microsoft.com/en-us/defender-office-365/anti-phishing-policies-about)

## ⚠️ Aviso

Todos os dados foram **sanitizados**: domínios, endereços e identificadores reais foram removidos ou parcialmente mascarados. O material tem fins educacionais e não substitui um procedimento pericial formal.

## 📬 Contato

- LinkedIn: [felipe-vieira-0338b329a](https://www.linkedin.com/in/felipe-vieira-0338b329a/)
- GitHub: [@felipe-xavier-cyber](https://github.com/felipe-xavier-cyber)
