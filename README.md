<!DOCTYPE html>
<html lang="pt">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Lista de Compras</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Inter', 'SF Pro Display', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
            background: #0a0e27;
            min-height: 100vh;
            padding: 0;
            margin: 0;
            color: #e4e4e7;
        }

        .barra-azul {
            background: #1a1f3a;
            width: 100%;
            height: 64px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-bottom: 1px solid #2a2f4a;
            backdrop-filter: blur(10px);
        }

        .barra-azul h2 {
            color: #00d9ff;
            font-size: 18px;
            font-weight: 500;
            letter-spacing: 0.5px;
            text-transform: uppercase;
        }

        .content-wrapper {
            display: flex;
            justify-content: center;
            align-items: flex-start;
            padding: 40px 20px;
            min-height: calc(100vh - 64px);
        }

        .container {
            background: #141829;
            border: 1px solid #2a2f4a;
            border-radius: 8px;
            padding: 32px;
            max-width: 700px;
            width: 100%;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
        }

        .input-container {
            display: flex;
            gap: 12px;
            margin-bottom: 32px;
        }

        #itemInput {
            flex: 1;
            padding: 14px 16px;
            background: #1a1f3a;
            border: 1px solid #2a2f4a;
            border-radius: 6px;
            font-size: 14px;
            color: #e4e4e7;
            transition: all 0.2s ease;
            font-family: inherit;
        }

        #itemInput::placeholder {
            color: #6b7280;
        }

        #itemInput:focus {
            outline: none;
            border-color: #00d9ff;
            background: #1f2439;
            box-shadow: 0 0 0 3px rgba(0, 217, 255, 0.1);
        }

        #enviarBtn {
            padding: 14px 24px;
            background: #00d9ff;
            color: #0a0e27;
            border: none;
            border-radius: 6px;
            font-size: 14px;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.2s ease;
            letter-spacing: 0.3px;
        }

        #enviarBtn:hover {
            background: #00b8d9;
            transform: translateY(-1px);
            box-shadow: 0 4px 12px rgba(0, 217, 255, 0.3);
        }

        #enviarBtn:active {
            transform: translateY(0);
        }

        .table-container {
            overflow-x: auto;
            border: 1px solid #2a2f4a;
            border-radius: 6px;
        }

        table {
            width: 100%;
            border-collapse: collapse;
        }

        thead {
            background: #1a1f3a;
            border-bottom: 1px solid #2a2f4a;
        }

        th {
            padding: 14px 16px;
            text-align: left;
            font-weight: 500;
            font-size: 12px;
            color: #9ca3af;
            letter-spacing: 0.5px;
            text-transform: uppercase;
        }

        td {
            padding: 16px;
            border-bottom: 1px solid #2a2f4a;
            font-size: 14px;
            color: #e4e4e7;
        }

        tbody tr {
            transition: background-color 0.15s ease;
        }

        tbody tr:hover {
            background-color: #1a1f3a;
        }

        tbody tr:last-child td {
            border-bottom: none;
        }

        .empty-message {
            text-align: center;
            padding: 48px;
            color: #6b7280;
            font-size: 14px;
        }

        .btn-remover {
            padding: 4px 10px;
            background: transparent;
            color: #6b7280;
            border: 1px solid transparent;
            border-radius: 4px;
            font-size: 18px;
            cursor: pointer;
            transition: all 0.2s ease;
            font-weight: 300;
            line-height: 1;
            width: 28px;
            height: 28px;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .btn-remover:hover {
            background: rgba(239, 68, 68, 0.1);
            color: #ef4444;
            border-color: rgba(239, 68, 68, 0.3);
        }

        .btn-remover:active {
            transform: scale(0.95);
        }

        .acao-cell {
            text-align: center;
            width: 60px;
        }

        .numero-cell {
            text-align: center;
            width: 50px;
            font-weight: 500;
            color: #00d9ff;
            font-size: 13px;
        }
    </style>
</head>
<body>
    <div class="barra-azul">
        <h2>Lista de Compras</h2>
    </div>
    
    <div class="content-wrapper">
        <div class="container">
        <div class="input-container">
            <input 
                type="text" 
                id="itemInput" 
                placeholder="Adicionar item..."
                autocomplete="off"
            >
            <button id="enviarBtn">Adicionar</button>
        </div>

        <div class="table-container">
            <table id="listaTable">
                <thead>
                    <tr>
                        <th class="numero-cell">#</th>
                        <th>Item</th>
                        <th class="acao-cell">Ações</th>
                    </tr>
                </thead>
                <tbody id="listaBody">
                    <tr>
                        <td class="empty-message" colspan="3">Lista vazia</td>
                    </tr>
                </tbody>
            </table>
        </div>
        </div>
    </div>

    <script>
        const itemInput = document.getElementById('itemInput');
        const enviarBtn = document.getElementById('enviarBtn');
        const listaBody = document.getElementById('listaBody');

        function adicionarItem() {
            const itemTexto = itemInput.value.trim();
            
            if (itemTexto === '') {
                alert('Por favor, digite um item antes de enviar!');
                return;
            }

            // Remove a mensagem de lista vazia se existir
            if (listaBody.querySelector('.empty-message')) {
                listaBody.innerHTML = '';
            }

            // Cria uma nova linha na tabela
            const novaLinha = document.createElement('tr');
            
            // Célula de numeração
            const celulaNumero = document.createElement('td');
            celulaNumero.className = 'numero-cell';
            novaLinha.appendChild(celulaNumero);
            
            // Célula do item
            const novaCelula = document.createElement('td');
            novaCelula.textContent = itemTexto;
            novaLinha.appendChild(novaCelula);
            
            // Célula do botão remover
            const celulaAcao = document.createElement('td');
            celulaAcao.className = 'acao-cell';
            const btnRemover = document.createElement('button');
            btnRemover.className = 'btn-remover';
            btnRemover.textContent = '×';
            btnRemover.addEventListener('click', function() {
                removerItem(novaLinha);
            });
            celulaAcao.appendChild(btnRemover);
            novaLinha.appendChild(celulaAcao);
            
            listaBody.appendChild(novaLinha);

            // Atualiza a numeração de todos os itens
            atualizarNumeracao();

            // Limpa o input e foca nele novamente
            itemInput.value = '';
            itemInput.focus();
        }

        function removerItem(linha) {
            linha.remove();
            
            // Atualiza a numeração de todos os itens
            atualizarNumeracao();
            
            // Se a lista ficar vazia, mostra a mensagem
            if (listaBody.children.length === 0) {
                const trVazio = document.createElement('tr');
                const tdVazio = document.createElement('td');
                tdVazio.className = 'empty-message';
                tdVazio.setAttribute('colspan', '3');
                tdVazio.textContent = 'Lista vazia';
                trVazio.appendChild(tdVazio);
                listaBody.appendChild(trVazio);
            }
        }

        function atualizarNumeracao() {
            const linhas = listaBody.querySelectorAll('tr');
            let contador = 1;
            linhas.forEach((linha) => {
                // Ignora linhas com mensagem vazia
                if (!linha.querySelector('.empty-message')) {
                    const celulaNumero = linha.querySelector('.numero-cell');
                    if (celulaNumero) {
                        celulaNumero.textContent = contador;
                        contador++;
                    }
                }
            });
        }

        // Adiciona item ao clicar no botão
        enviarBtn.addEventListener('click', adicionarItem);

        // Adiciona item ao pressionar Enter no input
        itemInput.addEventListener('keypress', function(event) {
            if (event.key === 'Enter') {
                adicionarItem();
            }
        });
    </script>
</body>
</html>
