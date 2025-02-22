Perfeito! Você pode usar um formato semelhante para estruturar o seu módulo. Vou ajustar os exemplos anteriores para seguir esse estilo.

### Módulo 1: Save Trade NPC

1. **Crie uma nova pasta chamada `npcTradeCapture`** dentro da pasta `modules` do seu OTClient.

2. **Crie um arquivo chamado `module.otmod`** dentro da pasta `npcTradeCapture` com o seguinte conteúdo:
```yaml
Module {
    name: npcTradeCapture,
    description: Captura informações da janela de trade dos NPCs,
    author: SeuNome,
    version: 1.1,
    sandboxed: true,
    scripts: [ npcTradeCapture ],
    @onLoad: init(),
    @onUnload: terminate()
}
```

3. **Crie um arquivo chamado `npcTradeCapture.lua`** dentro da mesma pasta com o seguinte conteúdo:
```lua
local isEnabled = false

local function saveNpcTradeInfo(npcName, tradeItems)
    if not isEnabled then return end
    
    local fileName = npcName:gsub("%s+", "_") .. "_trade_info.txt"
    local file = io.open(fileName, "w")
    if file then
        for _, item in ipairs(tradeItems) do
            file:write("IdItem: " .. item.id .. ", ")
            file:write("NameNpc: " .. npcName .. ", ")
            file:write("NameItem: " .. item.name .. ", ")
            file:write("ItemValue: " .. item.price .. ", ")
            file:write("ItemWeight: " .. item.weight .. ".\n")
            
            -- Salvando sprite do item com o ID do item
            local spriteFileName = item.id .. "_sprite.png"
            local sprite = g_sprites.getSprite(item.id)
            if sprite then
                sprite:saveToPng(spriteFileName)
            else
                print("Erro ao obter o sprite para o item: " .. item.name)
            end
        end
        file:close()
    else
        print("Erro ao abrir o arquivo para escrita.")
    end
end

function onOpenNpcTradeWindow(npcName, tradeItems)
    saveNpcTradeInfo(npcName, tradeItems)
end

function toggleModule()
    isEnabled = not isEnabled
    local status = isEnabled and "ativado" or "desativado"
    print("Módulo npcTradeCapture " .. status .. ".")
    g_game.enableFeature(GameNpcTrade)
end

function init()
    local button = g_ui.createWidget("Button", modules.game_interface.getRootPanel())
    button:setText("Toggle Trade Capture")
    button:setWidth(120)
    button:setHeight(30)
    button:breakAnchors()
    button:addAnchor(AnchorBottom, "parent", AnchorBottom, -50)
    button:addAnchor(AnchorRight, "parent", AnchorRight, -10)
    button.onClick = toggleModule
    
    connect(g_game, { onOpenNpcTradeWindow = onOpenNpcTradeWindow })
end

function terminate()
    disconnect(g_game, { onOpenNpcTradeWindow = onOpenNpcTradeWindow })
end
```

### Módulo 2: NPC Trade Analyzer

1. **Crie uma nova pasta chamada `npcTradeAnalyzer`** dentro da pasta `modules` do seu OTClient.

2. **Crie um arquivo chamado `module.otmod`** dentro da pasta `npcTradeAnalyzer` com o seguinte conteúdo:
```yaml
Module {
    name: npcTradeAnalyzer,
    description: Analisa o valor dos itens vendáveis para NPCs,
    author: SeuNome,
    version: 1.0,
    sandboxed: true,
    scripts: [ npcTradeAnalyzer ],
    @onLoad: init(),
    @onUnload: terminate()
}
```

3. **Crie um arquivo chamado `npcTradeAnalyzer.lua`** dentro da mesma pasta com o seguinte conteúdo:
```lua
local function analyzeNpcTrade(npcName, tradeItems)
    local totalValue = 0
    for _, item in ipairs(tradeItems) do
        if item.price > 0 then
            totalValue = totalValue + item.price
        else
            print("Item não comprável por NPC: " .. item.name)
            displayErrorMessage(item.name .. " não é comprável por NPCs.")
        end
    end
    print("Total Value: " .. totalValue)
    displayTotalValue(totalValue)
end

function onOpenNpcTradeWindow(npcName, tradeItems)
    analyzeNpcTrade(npcName, tradeItems)
end

function displayErrorMessage(message)
    local window = g_ui.createWidget("PopupWindow", rootWidget)
    window:setText("Erro de Trade NPC")
    window:setWidth(300)
    window:setHeight(100)

    local label = g_ui.createWidget("Label", window)
    label:setText(message)
    label:resizeToText()

    local button = g_ui.createWidget("Button", window)
    button:setText("Ok")
    button:breakAnchors()
    button:addAnchor(AnchorBottom, "parent", AnchorBottom)
    button:addAnchor(AnchorHorizontalCenter, "parent", AnchorHorizontalCenter)
    button.onClick = function()
        window:destroy()
    end

    window:raise()
    window:focus()
end

function displayTotalValue(value)
    local window = g_ui.createWidget("PopupWindow", rootWidget)
    window:setText("Valor Total")
    window:setWidth(300)
    window:setHeight(100)

    local label = g_ui.createWidget("Label", window)
    label:setText("Valor total a ser recebido: " .. value)
    label:resizeToText()

    local button = g_ui.createWidget("Button", window)
    button:setText("Ok")
    button:breakAnchors()
    button:addAnchor(AnchorBottom, "parent", AnchorBottom)
    button:addAnchor(AnchorHorizontalCenter, "parent", AnchorHorizontalCenter)
    button.onClick = function()
        window:destroy()
    end

    window:raise()
    window:focus()
end

function init()
    connect(g_game, { onOpenNpcTradeWindow = onOpenNpcTradeWindow })
end

function terminate()
    disconnect(g_game, { onOpenNpcTradeWindow = onOpenNpcTradeWindow })
end
```

Agora, ambos os módulos seguem o formato que você mencionou, tornando-os mais consistentes com os outros módulos que você pode ter no OTClient. Se precisar de mais alguma coisa ou tiver outras dúvidas, estou à disposição para ajudar!
