# US-10: Trocar de restaurante com sacola

**Como** cliente, **quero** ser avisado ao mudar de restaurante com itens na sacola, **para** não perder a seleção. (opcional no MVP)

- Referência visual: nenhuma tela do handoff mocka esse aviso. A necessidade vem da regra de negócio implícita no estado do carrinho: Restaurante/Cardápio (tela 02) permite adicionar itens de um restaurante à sacola, e o `cartStore.ts` (`mandaai-arquitetura.md` seção 9) guarda um único `restaurantId` por sacola.
- Estados envolvidos: sacola vazia (troca livre, sem aviso), sacola com itens de outro restaurante (aviso de substituição), usuário confirma troca (sacola antiga é limpa) ou cancela (permanece no restaurante atual).
- Entidades de domínio: carrinho client-side (`useCartStore`, Zustand) — campo `restaurantId`; `Restaurant`.
- Escopo: entra o aviso/confirmação antes de substituir a sacola. `[DECISÃO PENDENTE]`: formato do aviso (modal, toast com desfazer, etc.) não está definido — não há mockup de referência. Esta US é opcional no MVP.
