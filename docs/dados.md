\# Onde cada dado fica guardado



Regra: \*\*quem é o usuário e o que ele pode fazer\*\* fica no Keycloak.

\*\*O que o usuário fez e possui\*\* fica no banco da aplicação.



| Dado | Onde fica | Por quê |

|---|---|---|

| username | Keycloak | identifica o usuário |

| senha | Keycloak (hash) | nunca no banco da aplicação |

| e-mail | Keycloak | lido do token, sem duplicar |

| nome e sobrenome | Keycloak | validação feita no User profile |

| roles (aluno/admin) | Keycloak | controla autorização |

| foto e bio | Banco da aplicação | perfil estendido |

| dados do negócio | Banco da aplicação | domínio do sistema |

| ID do usuário (`sub`) | Os dois | liga o Keycloak ao banco |



\## Ao criar um dado novo

1\. Serve para identificar, autenticar ou autorizar? Então vai para o Keycloak.

2\. É dado do negócio? Então vai para o banco da aplicação.

3\. Adicione uma linha nesta tabela.

