<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fechamento com 3 Fixas, Sequência Máx 2 e Check de Uso</title>
    <style>
        /* Estilos CSS */
        body {
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
            background-color: #f4f4f9;
        }

        h1 {
            color: #333;
        }

        /* Estilo da Tabela de Dezenas (Calendário) */
        table {
            border-collapse: collapse;
            width: 90%;
            max-width: 700px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            background-color: #fff;
            margin-top: 20px;
        }

        th, td {
            border: 1px solid #ddd;
            padding: 0;
            text-align: center;
            height: 70px;
            width: 14.28%;
        }
        
        td.bloqueada {
            background-color: #eee;
        }

        /* Estilo Padrão do Botão de Dezena */
        .cell-button {
            width: 100%;
            height: 100%;
            border: none;
            background-color: transparent;
            color: #17a2b8;
            font-size: 20px;
            font-weight: bold;
            cursor: pointer;
            transition: background-color 0.3s, color 0.3s;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .cell-button:hover {
            background-color: #e2f4f7;
            color: #0d8b9e;
        }
        
        /* ESTILO: Botões Fixos Selecionados */
        .selecionada {
            background-color: #28a745 !important;
            color: white !important;
            border: 2px solid #1e7e34;
            box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.5);
        }
        
        .selecionada:hover {
            background-color: #1e7e34 !important; 
        }
        
        /* Estilo do Painel de Controle e Display */
        .controls {
            margin-top: 20px;
            padding: 15px;
            border: 1px solid #dc3545;
            background-color: #fff0f3;
            width: 90%;
            max-width: 700px;
            text-align: left;
        }

        .fechamento-display {
            margin-top: 20px;
            padding: 15px;
            border: 2px dashed #007bff; 
            background-color: #eaf3ff;
            min-height: 100px;
            width: 90%;
            max-width: 700px;
            text-align: left;
        }

        .fechamento-display h3 {
            margin-top: 0;
            color: #007bff;
        }

        .fixed-numbers {
            font-weight: bold;
            color: #28a745;
        }
        
        /* Estilos da Tabela de Volantes */
        #tabela-volantes {
            width: 100%;
            border-collapse: collapse;
            margin-top: 10px;
        }

        #tabela-volantes th, #tabela-volantes td {
            border: 1px solid #cce5ff;
            padding: 8px;
            text-align: center;
            height: auto;
            width: auto;
        }

        #tabela-volantes th {
            background-color: #007bff;
            color: white;
            font-weight: normal;
        }
        
        /* NOVO ESTILO: Botão de Check */
        .check-button {
            background-color: #f0ad4e; /* Amarelo/Laranja - Não Usado */
            color: white;
            border: none;
            padding: 8px 12px;
            cursor: pointer;
            border-radius: 4px;
            font-weight: bold;
            transition: background-color 0.3s;
        }

        .check-button.volante-usado {
            background-color: #5cb85c; /* Verde - Usado */
            content: '✓ Checado';
        }
        
        #input-volantes button:disabled {
            background-color: #ccc;
            cursor: not-allowed;
        }
    </style>
</head>
<body>

    <h1>Fechamento: Selecione 3 Dezenas Fixas</h1>
    <p>1. Clique em **três dezenas** (1-31) na tabela para fixá-las.</p>
    <p>2. Os volantes terão **máximo de 2 dezenas sequenciais**.</p>

    <table>
        <thead>
            <tr>
                <th colspan="7">Selecione suas 3 Dezenas Fixas</th>
            </tr>
        </thead>
        <tbody>
            <script>
                // Geração da Tabela (5x7)
                const colunas = 7;
                const totalCelulas = 35; 
                const maxBotao = 31; 

                document.write('<tr>');
                for (let i = 1; i <= totalCelulas; i++) {
                    if ((i - 1) % colunas === 0 && i !== 1) {
                        document.write('</tr><tr>');
                    }
                    
                    if (i <= maxBotao) {
                        document.write('<td>');
                        document.write(`<button class="cell-button" data-dezena="${i}" onclick="selecionarFixa(${i}, this)">${i}</button>`);
                        document.write('</td>');
                    } else {
                        document.write('<td class="bloqueada"></td>');
                    }
                }
                document.write('</tr>');
            </script>
        </tbody>
    </table>

    <div class="controls" id="controls-section">
        <p id="info-fixas">Fixas Selecionadas (0/3):</p>
        <div id="input-volantes">
            <label for="num-volantes">Quantos volantes (vetores) deseja criar (max 20)? </label>
            <input type="number" id="num-volantes" min="1" max="20" value="10" style="width: 60px;">
            <button id="btn-gerar" onclick="gerarVolantes()" disabled>Gerar Volantes</button>
        </div>
    </div>

    <div class="fechamento-display" id="fechamento-display">
        <h3>Volantes Gerados</h3>
        <p>Gere os volantes após selecionar 3 dezenas fixas.</p>
    </div>

    <script>
        // Variáveis globais para armazenar o estado
        let dezenasFixas = [];
        const MAX_FIXAS = 3;
        const MAX_DEZENA = 31;

        /**
         * @returns {boolean} True se houver 3 ou mais dezenas sequenciais.
         */
        function temSequenciaDeTres(volante) {
            const sortedVolante = [...volante].sort((a, b) => a - b);
            
            for (let i = 0; i < sortedVolante.length - 2; i++) {
                if (sortedVolante[i] + 1 === sortedVolante[i + 1] && sortedVolante[i] + 2 === sortedVolante[i + 2]) {
                    return true;
                }
            }
            return false;
        }

        /**
         * Gerencia a seleção/desseleção das 3 dezenas fixas.
         */
        function selecionarFixa(dezena, button) {
            const index = dezenasFixas.indexOf(dezena);

            if (index > -1) {
                dezenasFixas.splice(index, 1);
                button.classList.remove('selecionada');
            } else if (dezenasFixas.length < MAX_FIXAS) {
                dezenasFixas.push(dezena);
                button.classList.add('selecionada');
            } else {
                alert(`Você já selecionou o máximo de ${MAX_FIXAS} dezenas fixas.`);
                return;
            }
            
            dezenasFixas.sort((a, b) => a - b);
            atualizarControles();
        }
        
        /**
         * Atualiza o painel de controle e habilita/desabilita o botão Gerar.
         */
        function atualizarControles() {
            const infoDisplay = document.getElementById('info-fixas');
            const btnGerar = document.getElementById('btn-gerar');
            
            infoDisplay.innerHTML = `
                Fixas Selecionadas (${dezenasFixas.length}/${MAX_FIXAS}): 
                <span class="fixed-numbers">${dezenasFixas.join(', ')}</span>
            `;
            
            if (dezenasFixas.length === MAX_FIXAS) {
                btnGerar.disabled = false;
            } else {
                btnGerar.disabled = true;
            }
        }
        
        /**
         * NOVO: Alterna o estado de "usado" do volante.
         */
        function toggleVolanteUso(button) {
            if (button.classList.contains('volante-usado')) {
                button.classList.remove('volante-usado');
                button.textContent = 'Usar';
            } else {
                button.classList.add('volante-usado');
                button.textContent = '✓ Usado';
            }
        }


        /**
         * Gera os volantes (vetores), completando até 7 dezenas, aplicando a restrição de sequência.
         */
        function gerarVolantes() {
            if (dezenasFixas.length !== MAX_FIXAS) {
                alert(`Você deve selecionar exatamente ${MAX_FIXAS} dezenas fixas.`);
                return;
            }

            const numVolantes = parseInt(document.getElementById('num-volantes').value);
            
            if (isNaN(numVolantes) || numVolantes < 1 || numVolantes > 20) {
                alert("Por favor, insira um número de volantes válido (1 a 20).");
                return;
            }

            // Dezenas disponíveis para preenchimento (universo - fixas)
            const dezenasParaCompletar = [];
            for (let i = 1; i <= MAX_DEZENA; i++) {
                if (!dezenasFixas.includes(i)) {
                    dezenasParaCompletar.push(i);
                }
            }
            
            let poolComplementar = [...dezenasParaCompletar];

            let volantesGerados = [];
            const dezenasRestantesNecessarias = 7 - MAX_FIXAS; 
            const MAX_TENTATIVAS = 1000;

            const gerarComplemento = (pool) => {
                const complemento = [];
                const poolTemp = [...pool];
                
                // Embaralha a pool (Fisher-Yates)
                for (let i = poolTemp.length - 1; i > 0; i--) {
                    const j = Math.floor(Math.random() * (i + 1));
                    [poolTemp[i], poolTemp[j]] = [poolTemp[j], poolTemp[i]];
                }

                return poolTemp.slice(0, dezenasRestantesNecessarias);
            };

            while (volantesGerados.length < numVolantes) {
                let tentativas = 0;
                let volanteValido = false;

                while (!volanteValido && tentativas < MAX_TENTATIVAS) {
                    tentativas++;
                    
                    const complemento = gerarComplemento(poolComplementar);
                    let volante = [...dezenasFixas, ...complemento];
                    
                    volante = [...new Set(volante)].slice(0, 7).sort((a, b) => a - b);
                    
                    if (volante.length === 7 && !temSequenciaDeTres(volante)) {
                        volantesGerados.push(volante);
                        volanteValido = true;
                    }
                }

                if (tentativas === MAX_TENTATIVAS) {
                    console.error("Limite de tentativas atingido.");
                    alert(`Aviso: Foi possível gerar apenas ${volantesGerados.length} volantes com a restrição de sequência imposta. O limite de tentativas foi atingido.`);
                    break;
                }
            }

            // --- Geração da Tabela HTML ---
            let tabelaHtml = `
                <table id="tabela-volantes">
                    <thead>
                        <tr>
                            <th>Volante</th>
                            <th>Dezenas (7)</th>
                            <th>Usado?</th> </tr>
                    </thead>
                    <tbody>
            `;

            volantesGerados.forEach((volante, index) => {
                const volanteNumero = index + 1;
                const dezenasFormatadas = volante.map(dezena => {
                    if (dezenasFixas.includes(dezena)) {
                        return `<span class="fixed-numbers">${dezena}</span>`;
                    }
                    return dezena;
                }).join(', ');
                
                tabelaHtml += `
                    <tr>
                        <td>${volanteNumero}</td>
                        <td style="text-align: left;">${dezenasFormatadas}</td>
                        <td>
                            <button class="check-button" onclick="toggleVolanteUso(this)">Usar</button>
                        </td>
                    </tr>
                `;
            });

            tabelaHtml += `
                    </tbody>
                </table>
            `;
            // --- Fim da Geração da Tabela HTML ---

            const display = document.getElementById('fechamento-display');
            display.innerHTML = `
                <h3>Volantes Gerados (${volantesGerados.length} vetores)</h3>
                <p>Dezenas Fixas: <span class="fixed-numbers">${dezenasFixas.join(', ')}</span></p>
                <p>⚠️ Regra aplicada: **Máximo de 2 dezenas sequenciais** por volante.</p>
                ${tabelaHtml}
                <p>O botão **"Usar"** serve para marcar os volantes que você já preencheu. Clique para alternar para **"✓ Usado"**.</p>
            `;
        }
        
        document.addEventListener('DOMContentLoaded', atualizarControles);
    </script>

</body>
</html>
