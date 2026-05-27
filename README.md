
const fs = require('fs');
const path = require('path');
const axios = require('axios');
const cheerio = require('cheerio');

// Configuração dos posts que queremos gerar com base nas fontes
const fontesDeDados = [
    {
        id: 'decreto-9640',
        url: 'https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/decreto/d9640.htm',
        tipo: 'Artigo de Blog / Informativo',
        hashtags: '#SucessaoRural #DireitoNoCampo #AgriculturaFamiliar #Decreto9640'
    },
    {
        id: 'conferencia-mda-2026',
        url: 'https://www.gov.br/mda/pt-br/noticias/2026/03/3a-conferencia-nacional-de-desenvolvimento-rural-sustentavel-e-solidario-destaca-impacto-dos-programas-do-mda-na-vida-de-agricultoras',
        tipo: 'Post para Redes Sociais',
        hashtags: '#MulheresRurais #MDA2026 #DesenvolvimentoSustentavel #AgriculturaFamiliar'
    }
];

// Função principal assíncrona para capturar os dados e salvar no repositório
async function iniciarGeradorConteudo() {
    console.log('🚀 Iniciando o Gerador de Conteúdo Digital...');
    console.log('--------------------------------------------------');

    for (const fonte of fontesDeDados) {
        try {
            console.log(`🌐 Buscando dados de: ${fonte.url}`);
            
            // Faz a requisição HTTP para a fonte
            const { data } = await axios.get(fonte.url, {
                headers: { 'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64)' }
            });
            const $ = cheerio.load(data);

            // Captura o título da página (adaptando para portais de governo)
            let tituloOriginal = $('h1').text().trim() || $('title').text().trim();
            // Limpa quebras de linha excessivas comuns em sites governamentais
            tituloOriginal = tituloOriginal.replace(/\s+/g, ' '); 

            // Estrutura de pastas dentro do repositório do GitHub
            const pastaPost = path.join(__dirname, 'content-hub', fonte.id);
            const pastaImagens = path.join(pastaPost, 'images');

            // Cria o diretório do post e a subpasta de imagens se não existirem
            fs.mkdirSync(pastaImagens, { recursive: true });

            // Criação do corpo do arquivo Markdown compilando as informações para a internet
            const corpoMarkdown = [
                `# 📝 Conteúdo Gerado Automaticamente`,
                `> **Origem da Fonte:** [Acessar Link Oficial](${fonte.url})`,
                `> **Formato Alvo:** ${fonte.tipo}`,
                `---`,
                `## 📌 Título Base Extraído`,
                `${tituloOriginal}`,
                `---`,
                `## ✍️ Sugestão de Cópia / Legenda`,
                `Confira as últimas atualizações sobre o desenvolvimento rural sustentável! Com base nos dados oficiais, este movimento reforça o papel da nossa comunidade no cenário atual. Veja os detalhes completos no link da bio!`,
                `\n${fonte.hashtags}`,
                `---`,
                `## 🖼️ Ativos de Mídia`,
                `- **Instruções:** Salve o arquivo de imagem correspondente na pasta \`/images\` deste diretório para subir tudo organizado no GitHub.`
            ].join('\n\n');

            // Salva o arquivo final
            const caminhoArquivoFinal = path.join(pastaPost, 'README.md');
            fs.writeFileSync(caminhoArquivoFinal, corpoMarkdown, 'utf8');

            console.log(`📁 Pasta criada: /content-hub/${fonte.id}`);
            console.log(`💾 Arquivo gerado: /content-hub/${fonte.id}/README.md`);
            console.log('--------------------------------------------------');

        } catch (erro) {
            console.error(`❌ Erro ao processar a fonte [${fonte.id}]:`, erro.message);
            console.log('--------------------------------------------------');
        }
    }

    console.log('✨ Processo concluído! Seu repositório está atualizado e pronto para o Git Push.');
}

// Executa a automação
iniciarGeradorConteudo()const fs = require('fs');
const path = require('path');

// 1. Define o nome da pasta que será criada no repositório
const dirPath = path.join(__dirname, 'conteudos-posts');

// Cria a pasta caso ela não exista
if (!fs.existsSync(dirPath)) {
    fs.mkdirSync(dirPath, { recursive: true });
    console.log(`📁 Pasta "${dirPath}" criada com sucesso!`);
}

// 2. Define os conteúdos a serem salvos
const conteudos = [
    {
        filename: 'post-redes-sociais.md',
        content: `# Post para Redes Sociais\n\n🌾 Mulheres no topo do desenvolvimento rural! A 3ª Conferência Nacional de Desenvolvimento Rural Sustentável e Solidário destacou o impacto real das políticas do MDA na vida das agricultoras familiares. Garantir terra, crédito e autonomia para as mulheres é o caminho para um Brasil sem fome e com sustentabilidade. ✊✨\n\n#Ancoras: #AgriculturaFamiliar #MulheresRurais #MDA #Sustentabilidade`
    },
    {
        filename: 'resumo-decreto-conferencia.md',
        content: `# O Futuro do Campo: Decreto 9.640 e a Sucessão Rural no Brasil\n\nO Decreto nº 9.640/2018 institui o Plano Nacional de Juventude e Sucessão Rural, uma política pública crucial para garantir que as novas gerações permaneçam no campo com dignidade, acesso à terra, educação e renda.\n\nAliado a eventos como a recente 3ª Conferência Nacional de Desenvolvimento Rural Sustentável e Solidário, o plano reforça a necessidade de integrar jovens e mulheres na produção de alimentos saudáveis, fortalecendo a agricultura familiar e a segurança alimentar do país.`
    }
];

// 3. Grava os arquivos na pasta destinada
conteudos.forEach(item => {
    const filePath = path.join(dirPath, item.filename);
    fs.writeFileSync(filePath, item.content, 'utf8');
    console.log(`💾 Arquivo salvo: ${item.filename}`);
});

console.log('✅ Todos os arquivos foram gerados e estão prontos para o GitHub!');;
