// Aguarda o conteúdo da página ser totalmente carregado antes de executar o script
document.addEventListener('DOMContentLoaded', () => {

    // Seleciona os elementos da página (DOM) que serão manipulados
    const generateButton = document.getElementById('generateButton');
    const buttonText = document.getElementById('button-text');
    const resultsDiv = document.getElementById('results');
    const loader = document.getElementById('loader');
    const placeholder = document.getElementById('placeholder');
    const errorMessageDiv = document.getElementById('errorMessage');
    const resultsTextarea = document.getElementById('results-textarea');
    const copyButton = document.getElementById('copyButton');

    // Adiciona um "ouvinte" para o evento de clique no botão de gerar
    generateButton.addEventListener('click', async () => {
        // 1. Coleta os dados dos campos do formulário
        const apiKey = document.getElementById('apiKey').value.trim();
        const videoTopic = document.getElementById('videoTopic').value.trim();
        const keywords = document.getElementById('keywords').value.trim();
        const descriptionTone = document.getElementById('descriptionTone').value;

        // 2. Valida se os campos essenciais foram preenchidos
        if (!apiKey || !videoTopic) {
            showError("Por favor, preencha a Chave API e o Tema do Vídeo.");
            return; // Interrompe a execução se a validação falhar
        }

        // 3. Prepara a interface do usuário para o estado de "carregando"
        setLoading(true);
        clearResults();

        // 4. Constrói o prompt (instrução) para a inteligência artificial
        const systemPrompt = `Você é um especialista em SEO para YouTube e um copywriter sênior. Sua tarefa é criar UMA descrição de vídeo completa, otimizada e envolvente. A descrição deve seguir a seguinte estrutura:
1.  **Gancho Inicial:** Uma ou duas frases curtas e cativantes que resumem o valor do vídeo e geram curiosidade.
2.  **Parágrafo Detalhado:** Um parágrafo explicando o que o espectador aprenderá ou verá no vídeo. Incorpore as palavras-chave fornecidas de forma natural aqui.
3.  **Links Úteis (Opcional):** Uma seção com placeholders para links, como [SEU PRODUTO] ou [VÍDEO RELACIONADO].
4.  **Chamada para Ação (CTA):** Incentive o engajamento pedindo para curtir, se inscrever e comentar.
5.  **Hashtags:** Uma lista de 5 a 10 hashtags relevantes, incluindo as palavras-chave.

Adote o tom solicitado e retorne APENAS o texto da descrição, formatado com quebras de linha.`;
        
        const userPrompt = `Tema do Vídeo: "${videoTopic}"\nPalavras-chave: "${keywords}"\nTom da Descrição: "${descriptionTone}"`;

        // 5. Realiza a chamada para a API da Groq
        try {
            const response = await fetch('https://api.groq.com/openai/v1/chat/completions', {
                method: 'POST',
                headers: {
                    'Authorization': `Bearer ${apiKey}`,
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({
                    model: 'llama3-70b-8192',
                    messages: [
                        { role: 'system', content: systemPrompt },
                        { role: 'user', content: userPrompt }
                    ],
                    temperature: 0.7,
                    max_tokens: 1024,
                })
            });

            // Verifica se a resposta da API não foi bem-sucedida
            if (!response.ok) {
                const errorData = await response.json();
                throw new Error(errorData.error?.message || `Erro HTTP: ${response.status}`);
            }

            const data = await response.json();
            const content = data.choices[0]?.message?.content;

            // Se houver conteúdo na resposta, exibe o resultado
            if (content) {
                displayResult(content);
            } else {
                showError("A resposta da API estava vazia. Tente novamente.");
            }

        } catch (error) {
            // Captura e exibe qualquer erro que ocorra durante o processo
            console.error('Erro na API:', error);
            showError(`Ocorreu um erro: ${error.message}. Verifique sua chave API e a conexão.`);
        } finally {
            // Garante que o estado de "carregando" seja desativado, independentemente do resultado
            setLoading(false);
        }
    });
    
    // Adiciona o evento de clique para o botão de copiar
    copyButton.addEventListener('click', () => {
        const textToCopy = resultsTextarea.value;
        if (!textToCopy) return;

        const originalIcon = copyButton.innerHTML;
        // Tenta usar a API moderna de área de transferência
        navigator.clipboard.writeText(textToCopy).then(() => {
            // Muda o ícone para indicar sucesso
            copyButton.innerHTML = `<svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-green-500" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" /></svg>`;
            // Retorna ao ícone original após 2 segundos
            setTimeout(() => {
               copyButton.innerHTML = originalIcon;
            }, 2000);
        }).catch(err => {
            console.error('Falha ao copiar: ', err);
            // Se a API moderna falhar, usa o método antigo (fallback)
            const textarea = document.createElement('textarea');
            textarea.value = textToCopy;
            document.body.appendChild(textarea);
            textarea.select();
            try {
                document.execCommand('copy');
                copyButton.innerHTML = `<svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-green-500" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" /></svg>`;
                setTimeout(() => {
                   copyButton.innerHTML = originalIcon;
                }, 2000);
            } catch (fallbackErr) {
                console.error('Falha no fallback de cópia: ', fallbackErr);
            }
            document.body.removeChild(textarea);
        });
    });

    // Função para controlar o estado de carregamento da UI
    function setLoading(isLoading) {
        generateButton.disabled = isLoading;
        if (isLoading) {
            buttonText.textContent = 'Gerando...';
            loader.classList.remove('hidden');
            loader.classList.add('flex');
            placeholder.classList.add('hidden');
            errorMessageDiv.classList.add('hidden');
            resultsTextarea.classList.add('hidden');
            copyButton.classList.add('hidden');
        } else {
            buttonText.textContent = 'Gerar Descrição';
            loader.classList.add('hidden');
            loader.classList.remove('flex');
        }
    }

    // Função para limpar a área de resultados antes de uma nova geração
    function clearResults() {
        placeholder.classList.add('hidden');
        errorMessageDiv.classList.add('hidden');
        resultsTextarea.value = '';
        resultsTextarea.classList.add('hidden');
        copyButton.classList.add('hidden');
    }
    
    // Função para exibir uma mensagem de erro
    function showError(message) {
        clearResults();
        errorMessageDiv.textContent = message;
        errorMessageDiv.classList.remove('hidden');
        placeholder.classList.add('hidden');
    }

    // Função para exibir o resultado gerado pela IA
    function displayResult(content) {
        clearResults();
        resultsTextarea.value = content.trim();
        resultsTextarea.classList.remove('hidden');
        copyButton.classList.remove('hidden');
    }
});
