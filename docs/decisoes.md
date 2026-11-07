# Decisões técnicas

Resumo das decisões que mais pesaram no projeto, em contexto, decisão e resultado.

## 1. Manter o Firebase e tirar o desenvolvimento de produção

- **Contexto:** o custo mensal do Firestore saltou de poucos reais para mais de cem reais em um mês.
  A causa não era o uso real, e sim as leituras geradas por desenvolvimento apontado para produção.
- **Decisão:** em vez de migrar de banco, separei o dev. `npm run dev` sobe emuladores com uma cópia
  anonimizada, e um modo à parte conecta à produção com uma faixa vermelha bem visível.
- **Resultado:** o dia a dia deixou de gerar leituras cobradas, sem custo de migração e sem risco
  para clientes reais.

## 2. Anonimização que falha para o lado seguro

- **Contexto:** copiar a produção para uma máquina local envolve dados pessoais.
- **Decisão:** uso uma conta de serviço somente leitura, e o comando recusa continuar se ela tiver
  permissão de escrita. As regras de anonimização são por campo, e um verificador bloqueia a cópia
  inteira se algum valor real sobreviver. A mensagem de erro diz a coleção e o campo, nunca o valor.
- **Resultado:** nenhuma falha cai para produção, e a cópia anterior continua valendo se a nova for
  rejeitada.

## 3. Permissões nas Firestore Rules

- **Contexto:** vários setores (logística, conformidade, concierge, tecnologia) precisam de acessos
  diferentes aos mesmos dados.
- **Decisão:** uma matriz de setor, módulo e ação consultada pelas Rules. O frontend só reflete.
- **Custo:** cada requisição paga leituras extras para consultar perfil, setor e matriz. Aceitei
  porque o controle de acesso fica em um lugar só e dá para testar.

## 4. Apagar arquivo só depois de checar as referências

- **Contexto:** um mesmo arquivo pode ser usado por template, viagem e biblioteca.
- **Decisão:** uma fila de exclusão do Storage que confere as referências antes de apagar.
- **Custo conhecido:** a conferência lê muito o Firestore. Está mapeada como ponto quente para
  otimizar.

## 5. Aceitar o formato antigo dos dados em vez de migrar tudo

- **Contexto:** o sistema está em produção e tem documentos em formatos antigos.
- **Decisão:** os leitores aceitam o formato legado por adaptadores, e só os novos saves usam o formato
  novo. Quando uma migração é necessária, ela segue um plano documentado.
- **Resultado:** o schema evolui sem quebrar as viagens que já existem.
