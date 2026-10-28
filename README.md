Contexto: Recebimento de um e-mail suspeito solicitando alteração de dados bancários. O desafio era provar tecnicamente se o e-mail era falso (spoofing) ou se a conta do remetente havia sido invadida.

Ações Realizadas:

Extração e análise dos cabeçalhos (headers) do Microsoft 365.

Verificação dos registros de autenticação: SPF (pass), DKIM (pass) e DMARC (none).

Análise de metadados de roteamento: X-MS-Exchange-CrossTenant-id, Message-ID e servidores de origem (BN6PR11MB3921).

Conclusão Técnica: Foi atestada a autenticidade da origem (tenant Microsoft 365 legítimo da empresa). O e-mail não era um spoofing externo, mas um forte indício de Account Takeover (ATO).

Habilidades demonstradas: Email Security, Análise de SPF/DKIM/DMARC, Forense de Cabeçalhos, Detecção de ATO.
