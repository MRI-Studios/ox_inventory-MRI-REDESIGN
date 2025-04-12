# 📖 Documentação - Sistema de Raridade - Ox Inventory (Redesign by MRI Studios)

### Adicionar ID junto ao nome do jogador:
Procure pela função abaixo no ox_inventory em qbx > server.lua
```
playerData.name = ('%s %s'):format(playerData.charinfo.firstname, playerData.charinfo.lastname)
```

Substitua pela abaixo
```
playerData.name = ('%s %s | ID: %s'):format(playerData.charinfo.firstname, playerData.charinfo.lastname, playerData.citizenid)
```
---
## 🎨 Raridades Disponíveis
Os itens no inventário podem ter diferentes níveis de raridade, cada um podendo ser usado para diferenciar qualidade, efeitos ou valores.

### 🔹 Lista de Raridades:
| Chave (`metadata.rarity`) | Nome |
|------------------|------------|
| `common`        | Comum |
| `uncommon`      | Incomum |
| `rare`          | Raro |
| `epic`          | Épico |
| `legendary`     | Lendário |
| `special`       | Radiante |

---

## 🛠 Criando um Item com Raridade
Para adicionar um item com raridade, defina o campo `metadata` no arquivo `items.lua`.

### Exemplo de item com raridade pré-definida:
```lua
['diamond_ring'] = {
    label = 'Anel de Diamante',
    weight = 200,
    stack = false,
    close = true,
    description = 'Um anel feito de diamante puro.',
    metadata = {
        rarity = 'legendary' -- Define a raridade do item
    }
}
```
📌 Isso significa que qualquer "diamond_ring" criado no jogo já terá a raridade `legendary`.

---

## 🛒 Criando Lojas que Vendem Itens com Raridade
Para vender um item com raridade pré-definida, adicione a chave `metadata` na configuração da loja.

### Exemplo de Loja (`shops.lua`):
```lua
['luxury_jewelry'] = {
    name = 'Joalheria de Luxo',
    blip = {
        id = 617,
        colour = 5,
        scale = 0.8
    },
    inventory = {
        { 
            name = 'diamond_ring', 
            price = 5000,
            metadata = { rarity = 'legendary' } -- O item comprado já terá essa raridade
        }
    },
    locations = {
        vec3(-622.0, -230.7, 38.06),
    }
}
```
📌 Agora, toda vez que um jogador comprar o "diamond_ring", ele já virá como um item Lendário.


---

## ⚙️ Personalizando Funcionalidades para Diferentes Raridades
Itens de diferentes raridades podem ter efeitos distintos dentro do servidor. **Isso deve ser configurado pelo cliente** conforme sua necessidade.

## ✅ `createItem` – Definindo Raridade Aleatória ao Criar um Item

Use este hook para atribuir uma raridade automaticamente ao criar um item, como por exemplo lootboxes.

```lua
exports.ox_inventory:registerHook('createItem', function(payload)
    local itemName = payload.item.name
    local metadata = payload.metadata or {}

    if not metadata.rarity and itemName == 'lootbox' then
        local roll = math.random(1, 100)
        if roll <= 50 then
            metadata.rarity = 'common'
        elseif roll <= 80 then
            metadata.rarity = 'uncommon'
        elseif roll <= 95 then
            metadata.rarity = 'rare'
        elseif roll <= 99 then
            metadata.rarity = 'epic'
        else
            metadata.rarity = 'legendary'
        end
    end

    return metadata
end, {
    print = true,
    itemFilter = {
        lootbox = true
    }
})
```
## 🔄 `swapItems` – Bloquear Movimentação de Itens com Raridade “special”

Esse hook impede que itens raríssimos sejam movidos entre inventários, como porta-malas, cofres ou entre jogadores.

```lua
exports.ox_inventory:registerHook('swapItems', function(payload)
    local fromRarity = payload.fromSlot.metadata and payload.fromSlot.metadata.rarity
    if fromRarity == 'special' then
        TriggerClientEvent('ox_lib:notify', payload.source, {
            title = 'Item Bloqueado',
            description = 'Esse item é muito raro para ser movido.',
            type = 'error'
        })
        return false
    end
end, {
    print = false,
    itemFilter = {
        special_item = true -- Nome interno do item
    }
})
```

---

## 📌 Observações

- Utilize `metadata.rarity` nos scripts de buffs, cura ou dano para personalizar o comportamento de cada item.
- Os nomes das raridades recomendadas são: `common`, `uncommon`, `rare`, `epic`, `legendary`, `special`.
- O uso de hooks garante segurança e flexibilidade sem alterar a estrutura principal do `ox_inventory`.

---
## 📌 Conclusão
✅ **Diferentes raridades podem ser aplicadas a itens** para criar diferenciação de qualidade.  
✅ **Lojas podem vender itens já com raridade configurada**.  
✅ **Cabe ao cliente definir os efeitos de raridade** para cada item, como aumentar recuperação de sede ou dano de armas.  

Se quiser acrescentar algo ou encontrar erros em nossa documentação, **entre em contato com o suporte**. 🚀
