<!DOCTYPE html>
<html lang="nl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Mestria Pro - Van Basis naar Expert</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { 
            font-family: ui-sans-serif, system-ui, -apple-system, sans-serif;
            background-color: #F8FAFC;
            color: #000000;
        }

        h1, h2, h3, p, span, button {
            color: #000000 !important;
        }

        .card-contrast {
            background-color: #FFFFFF;
            border: 3px solid #000000;
            box-shadow: 5px 5px 0px #000000;
        }

        .option-card {
            background-color: #FFFFFF;
            border: 3px solid #000000;
            transition: all 0.1s;
            cursor: pointer;
            -webkit-tap-highlight-color: transparent;
            box-shadow: 4px 4px 0px #000000;
        }

        .option-card:active {
            transform: translate(2px, 2px);
            box-shadow: 1px 1px 0px #000000;
        }

        .correct { 
            background-color: #22C55E !important; 
            box-shadow: none !important;
            transform: translate(2px, 2px);
        }
        .wrong { 
            background-color: #EF4444 !important; 
            box-shadow: none !important;
            transform: translate(2px, 2px);
        }

        .progress-bg {
            background-color: #FFFFFF;
            border: 3px solid #000000;
        }
    </style>
</head>
<body class="p-0 m-0">

    <div id="app">
        <!-- Dashboard -->
        <div id="dashboard" class="max-w-md mx-auto p-6 pb-24">
            <header class="flex justify-between items-start mb-8 pt-4">
                <div>
                    <h1 class="text-3xl font-black uppercase tracking-tighter">Mestria Pro</h1>
                    <p class="text-xs font-bold uppercase opacity-70 italic">Cursus Portugees</p>
                </div>
                <div class="text-right card-contrast p-2 px-4 rounded-xl">
                    <p class="text-[10px] font-black uppercase">XP Totaal</p>
                    <p id="display-xp" class="text-2xl font-black">0</p>
                </div>
            </header>
            
            <div id="levels-list" class="space-y-6"></div>

            <div class="mt-12 pt-8 border-t-4 border-black text-center">
                <div class="card-contrast rounded-3xl p-6 mb-20">
                    <h2 class="text-xl font-black uppercase mb-4">Support</h2>
                    <div class="space-y-4">
                        <input type="text" id="contact-name" placeholder="JE NAAM" class="w-full p-4 border-2 border-black rounded-xl font-bold outline-none focus:bg-yellow-50">
                        <textarea id="contact-msg" rows="2" placeholder="BERICHT..." class="w-full p-4 border-2 border-black rounded-xl font-bold outline-none focus:bg-yellow-50"></textarea>
                        <button onclick="sendEmail()" class="w-full bg-black text-white p-4 rounded-xl font-black uppercase tracking-widest active:bg-gray-800">Verstuur Bericht</button>
                    </div>
                </div>
            </div>
        </div>

        <!-- Quiz Interface -->
        <div id="quiz-overlay" class="hidden fixed inset-0 bg-white z-50 flex flex-col">
            <div class="p-4 flex items-center justify-between border-b-4 border-black">
                <button onclick="closeQuiz()" class="text-3xl font-black p-2">✕</button>
                <div class="flex-1 mx-4 progress-bg h-6 rounded-full overflow-hidden">
                    <div id="progress-bar" class="bg-blue-500 h-full" style="width: 0%"></div>
                </div>
                <div id="quiz-counter" class="text-sm font-black w-16 text-right">1/40</div>
            </div>
            
            <div class="flex-1 flex flex-col p-6 items-center justify-center bg-slate-50">
                <div class="w-full max-w-sm">
                    <div id="retry-label" class="hidden bg-yellow-300 border-2 border-black px-4 py-1 rounded-full text-xs font-black uppercase mb-4 inline-block">Herkansing: Focus</div>
                    <p id="q-category" class="text-xs font-black uppercase mb-1 opacity-60">Categorie</p>
                    <h2 id="q-text" class="text-2xl font-black mb-8 leading-tight">Vraag</h2>
                    <div id="options-container" class="space-y-4"></div>
                </div>
            </div>
        </div>

        <!-- Resultaten Scherm -->
        <div id="result-screen" class="hidden fixed inset-0 bg-yellow-400 z-[60] flex flex-col items-center justify-center p-8 text-center border-[12px] border-black">
            <div class="text-8xl mb-4">⭐</div>
            <h2 class="text-4xl font-black uppercase mb-2">Meesterschap!</h2>
            <p class="font-bold mb-8 text-lg">Dit niveau is nu 100% voltooid.</p>
            <div class="flex gap-4 mb-10">
                <div class="bg-white border-4 border-black p-4 rounded-2xl min-w-[120px] shadow-[4px_4px_0px_#000000]">
                    <p class="text-[10px] uppercase font-black">Bonus</p>
                    <p class="text-3xl font-black">+100</p>
                </div>
            </div>
            <button onclick="showDashboard()" class="w-full max-w-xs py-5 rounded-2xl bg-black text-white font-black uppercase tracking-tighter text-xl active:scale-95 transition-transform">Naar Dashboard</button>
        </div>
    </div>

    <script>
        const MASTER_CURRICULUM = {
            "A1": {
                cat: "Zelfstandige Naamwoorden",
                data: [
                    {p: "Pão", n: "Brood"}, {p: "Água", n: "Water"}, {p: "Sim", n: "Ja"}, {p: "Não", n: "Nee"},
                    {p: "Casa", n: "Huis"}, {p: "Cachorro", n: "Hond"}, {p: "Gato", n: "Kat"}, {p: "Obrigado", n: "Bedankt"},
                    {p: "Escola", n: "School"}, {p: "Carro", n: "Auto"}, {p: "Mesa", n: "Tafel"}, {p: "Cadeira", n: "Stoel"},
                    {p: "Livro", n: "Boek"}, {p: "Caneta", n: "Pen"}, {p: "Maçã", n: "Appel"}, {p: "Leite", n: "Melk"},
                    {p: "Vinho", n: "Wijn"}, {p: "Sol", n: "Zon"}, {p: "Lua", n: "Maan"}, {p: "Amigo", n: "Vriend"},
                    {p: "Mãe", n: "Moeder"}, {p: "Pai", n: "Vader"}, {p: "Filho", n: "Zoon"}, {p: "Filha", n: "Dochter"},
                    {p: "Peixe", n: "Vis"}, {p: "Carne", n: "Vlees"}, {p: "Ovo", n: "Ei"}, {p: "Rua", n: "Straat"},
                    {p: "Cidade", n: "Stad"}, {p: "Mar", n: "Zee"}, {p: "Praia", n: "Strand"}, {p: "Janela", n: "Raam"},
                    {p: "Porta", n: "Deur"}, {p: "Chave", n: "Sleutel"}, {p: "Relógio", n: "Horloge"}, {p: "Café", n: "Koffie"},
                    {p: "Açúcar", n: "Suiker"}, {p: "Sal", n: "Zout"}, {p: "Flor", n: "Bloem"}, {p: "Árvore", n: "Boom"}
                ]
            },
            "A2": {
                cat: "Korte Zinnen",
                data: [
                    {p: "Como estás?", n: "Hoe gaat het?"}, {p: "Tenho fome", n: "Ik heb honger"}, {p: "Estou cansado", n: "Ik ben moe"},
                    {p: "Onde é o hotel?", n: "Waar is het hotel?"}, {p: "Quanto custa?", n: "Hoeveel kost het?"}, {p: "Pode ajudar-me?", n: "Kunt u mij helpen?"},
                    {p: "Chamo-me Pedro", n: "Ik heet Pedro"}, {p: "Vou para casa", n: "Ik ga naar huis"}, {p: "Gosto de ler", n: "Ik hou van lezen"},
                    {p: "Está calor", n: "Het is warm"}, {p: "Preciso de dormir", n: "Ik moet slapen"}, {p: "Onde moras?", n: "Waar woon je?"},
                    {p: "Muito prazer", n: "Aangenaam kennis te maken"}, {p: "Até amanhã", n: "Tot morgen"}, {p: "Fala inglês?", n: "Spreekt u Engels?"},
                    {p: "Tenho sede", n: "Ik heb dorst"}, {p: "A conta, por favor", n: "De rekening, alstublieft"}, {p: "Onde é o banheiro?", n: "Waar is het toilet?"},
                    {p: "Que horas são?", n: "Hoe laat is het?"}, {p: "Sou da Holanda", n: "Ik kom uit Nederland"},
                    {p: "Faz sol hoje", n: "Het is zonnig vandaag"}, {p: "Estou feliz", n: "Ik ben gelukkig"}, {p: "Não percebo", n: "Ik begrijp het niet"},
                    {p: "Fala mais devagar", n: "Spreek langzamer"}, {p: "Onde fica a estação?", n: "Waar is het station?"}, {p: "Gosto disto", n: "Ik vind dit leuk"},
                    {p: "Não sei", n: "Ik weet het niet"}, {p: "Com certeza", n: "Zeker weten"}, {p: "Parabéns!", n: "Gefeliciteerd!"}, {p: "Bom fim de semana", n: "Fijn weekend"},
                    {p: "Como se diz?", n: "Hoe zeg je dat?"}, {p: "Estás pronto?", n: "Ben je klaar?"}, {p: "Vou viajar", n: "Ik ga reizen"}, {p: "Está fechado", n: "Het is gesloten"},
                    {p: "Está aberto", n: "Het is open"}, {p: "Que dia é hoje?", n: "Welke dag is het vandaag?"}, {p: "Esqueci-me", n: "Ik ben het vergeten"},
                    {p: "Desculpe", n: "Sorry"}, {p: "Sem problema", n: "Geen probleem"}, {p: "Bom trabalho", n: "Goed gewerkt"}
                ]
            },
            "B1": {
                cat: "Werkwoorden & Tijden",
                data: [
                    {p: "Eu fui ao mercado", n: "Ik ben naar de markt gegaan"}, {p: "Nós estamos a comer", n: "Wij zijn aan het eten"}, {p: "Eles falaram ontem", n: "Zij hebben gisteren gesproken"},
                    {p: "Eu comeria se...", n: "Ik zou eten als..."}, {p: "Espero que venhas", n: "Ik hoop dat je komt"}, {p: "Ela tem estado doente", n: "Zij is ziek geweest"},
                    {p: "Fazíamos isso sempre", n: "Dat deden we altijd"}, {p: "Vou ligar-te amanhã", n: "Ik zal je morgen bellen"}, {p: "Tu compraste o pão?", n: "Heb jij het brood gekocht?"},
                    {p: "Eles vão chegar tarde", n: "Zij zullen laat aankomen"}, {p: "Eu vi o filme", n: "Ik heb de film gezien"}, {p: "Nós tínhamos saído", n: "Wij waren al weggegaan"},
                    {p: "Quando eu chegar...", n: "Wanneer ik aankom..."}, {p: "Não faças isso", n: "Doe dat niet"}, {p: "Diga-me a verdade", n: "Vertel me de waarheid"},
                    {p: "Eles queriam trabalhar", n: "Zij wilden werken"}, {p: "Eu sabia disso", n: "Ik wist dat"}, {p: "Nós poderíamos ir", n: "Wij zouden kunnen gaan"},
                    {p: "Tu deves estudar", n: "Jij moet studeren"}, {p: "Ela trouxe flores", n: "Zij bracht bloemen"}, {p: "Esqueci-me da chave", n: "Ik ben de sleutel vergeten"},
                    {p: "Vais levar o carro?", n: "Ga je de auto meenemen?"}, {p: "Eu dou-te um presente", n: "Ik geef je een cadeau"}, {p: "Nós fazemos bolos", n: "Wij maken taarten"},
                    {p: "Eles estão a dormir", n: "Zij zijn aan het slapen"}, {p: "Eu pus a mesa", n: "Ik heb de tafel gedekt"}, {p: "Tu disseste a ele?", n: "Heb je het hem verteld?"},
                    {p: "Ela quis ficar", n: "Zij wilde blijven"}, {p: "Nós ouvimos música", n: "Wij hebben naar muziek geluisterd"}, {p: "O que aconteceu?", n: "Wat is er gebeurd?"},
                    {p: "Vou pensar nisso", n: "Ik ga erover nadenken"}, {p: "Ela ajudou-me", n: "Zij heeft me geholpen"}, {p: "Nós perdemos o trem", n: "Wij hebben de trein gemist"},
                    {p: "Eles encontraram-no", n: "Zij hebben hem gevonden"}, {p: "Eu espero por ti", n: "Ik wacht op je"}, {p: "Tu leste o jornal?", n: "Heb je de krant gelezen?"},
                    {p: "Ela escreveu a carta", n: "Zij schreef de brief"}, {p: "Nós pagamos a conta", n: "Wij betalen de rekening"}, {p: "Eles pediram ajuda", n: "Zij vroegen om hulp"}, {p: "Eu ganhei o jogo", n: "Ik heb het spel gewonnen"}
                ]
            },
            "B2": {
                cat: "Vloeiende Taal",
                data: [
                    {p: "Apesar da chuva, saímos", n: "Ondanks de regen zijn we weggegaan"}, {p: "Caso precises, liga-me", n: "Mocht je het nodig hebben, bel me"}, {p: "Oxalá tudo corra bem", n: "Hopelijk loopt alles goed af"},
                    {p: "É provável que aconteça", n: "Het is waarschijnlijk dat het gebeurt"}, {p: "Embora estivesse cansado...", n: "Hoewel ik moe was..."}, {p: "A situação é complexa", n: "De situatie is complex"},
                    {p: "Temos de resolver isto", n: "We moeten dit oplossen"}, {p: "Fiquei surpreendido", n: "Ik was verrast"}, {p: "Parece-me uma boa ideia", n: "Het lijkt me een goed idee"},
                    {p: "Não vale a pena", n: "Het is de moeite niet waard"}, {p: "Estou habituado a isto", n: "Ik ben hieraan gewend"}, {p: "Acredito que sim", n: "Ik geloof van wel"},
                    {p: "Depende das circunstâncias", n: "Het hangt van de omstandigheden af"}, {p: "Duvido que ele venha", n: "Ik betwijfel of hij komt"}, {p: "Faça como quiser", n: "Doe zoals u wilt"},
                    {p: "Gostaria de sugerir algo", n: "Ik zou graag iets willen suggereren"}, {p: "Não há outra saída", n: "Er is geen andere uitweg"}, {p: "Estou ansioso por...", n: "Ik kijk uit naar..."},
                    {p: "Aproveita o momento", n: "Geniet van het moment"}, {p: "Tenho andado ocupado", n: "Ik heb het druk gehad"}, {p: "Tanto faz para mim", n: "Het maakt mij niet uit"},
                    {p: "Pelo que eu sei...", n: "Voor zover ik weet..."}, {p: "Independentemente de...", n: "Ongeacht de..."}, {p: "O que sugeres fazer?", n: "Wat stel je voor om te doen?"},
                    {p: "Não me convence", n: "Het overtuigt me niet"}, {p: "Deixa-me pensar", n: "Laat me nadenken"}, {p: "A longo prazo", n: "Op de lange termijn"},
                    {p: "De qualquer forma", n: "In ieder geval"}, {p: "Por outro lado", n: "Aan de andere kant"}, {p: "Estou de acordo", n: "Ik ga akkoord"},
                    {p: "Afinal de contas", n: "Tenslotte"}, {p: "Faz sentido", n: "Het is logisch"}, {p: "Seja como for", n: "Hoe dan ook"},
                    {p: "Fico à espera", n: "Ik wacht af"}, {p: "De vez em quando", n: "Af en toe"}, {p: "É um prazer", n: "Het is een genoegen"},
                    {p: "Sinto muito por isso", n: "Het spijt me daarvoor"}, {p: "Podes contar comigo", n: "Je kunt op me rekenen"}, {p: "Vamos direto ao ponto", n: "Laten we direct ter zake komen"}, {p: "Estou a brincar", n: "Ik maak een grapje"}
                ]
            },
            "C1": {
                cat: "Expert & Idiomen",
                data: [
                    {p: "Estar com a pulga atrás da orelha", n: "Argwaan hebben"}, {p: "Chover no molhado", n: "Iets doen wat geen nut heeft"}, {p: "Dar o braço a torcer", n: "Je ongelijk toegeven"},
                    {p: "Pôr os pontos nos is", n: "De puntjes op de i zetten"}, {p: "Custar os olhos da cara", n: "Een fortuin kosten"}, {p: "Deitar água no fervido", n: "De gemoederen sussen"},
                    {p: "Andar nas nuvens", n: "In de wolken zijn"}, {p: "Bater na mesma tecla", n: "In herhaling vallen"}, {p: "Comer com os olhos", n: "Met de ogen eten"},
                    {p: "Dar pano para mangas", n: "Veel gespreksstof bieden"}, {p: "Estar nas suas sete quintas", n: "In je element zijn"}, {p: "Ficar a ver navios", n: "Met lege handen achterblijven"},
                    {p: "Ir por água abaixo", n: "In het water vallen (mislukken)"}, {p: "Meter os pés pelas mãos", n: "Zichzelf in de nesten werken"}, {p: "Não pregar olho", n: "Geen oog dichtdoen"},
                    {p: "Puxar a brasa à sua sardinha", n: "Eigen belang voorop stellen"}, {p: "Saltar à vista", n: "In het oog springen"}, {p: "Ter o rei na barriga", n: "Zich heel wat voelen"},
                    {p: "Ver-se grego para...", n: "Grote moeite hebben om..."}, {p: "A sorte está lançada", n: "De teerling is geworpen"}, {p: "Analisar minuciosamente", n: "Grondig analyseren"},
                    {p: "Abordagem pragmática", n: "Pragmatische benadering"}, {p: "Consequências nefastas", n: "Nadelige gevolgen"}, {p: "Em última análise", n: "Uiteindelijk"},
                    {p: "Estrutura hierárquica", n: "Hiërarchische structuur"}, {p: "Indispensável para o sucesso", n: "Onmisbaar voor succes"}, {p: "Metodologia rigorosa", n: "Strenge methodologie"},
                    {p: "No que concerne a...", n: "Wat betreft..."}, {p: "Ponto de discórdia", n: "Discussiepunt"}, {p: "Relevância socioeconómica", n: "Sociaaleconomische relevantie"},
                    {p: "Tomar providências", n: "Maatregelen nemen"}, {p: "Visão abrangente", n: "Brede visie"}, {p: "Zelo profissional", n: "Professionele ijver"},
                    {p: "Argumento convincente", n: "Overtuigend argument"}, {p: "Contexto histórico", n: "Historische context"}, {p: "Efeito dominó", n: "Domino-effect"},
                    {p: "Hipótese plausível", n: "Aannemelijke hypothese"}, {p: "Impacto ambiental", n: "Milieu-impact"}, {p: "Legislação em vigor", n: "Huidige wetgeving"}, {p: "Mudar de paradigma", n: "Van paradigma veranderen"}
                ]
            }
        };

        const LEVELS = [
            { id: "A1", name: "Level A1 - Basis", icon: "🌱", color: "bg-green-400" },
            { id: "A2", name: "Level A2 - Zinnen", icon: "🚲", color: "bg-blue-400" },
            { id: "B1", name: "Level B1 - Werkwoorden", icon: "🚗", color: "bg-purple-400" },
            { id: "B2", name: "Level B2 - Vloeiend", icon: "✈️", color: "bg-orange-400" },
            { id: "C1", name: "Level C1 - Expert", icon: "🚀", color: "bg-red-400" }
        ];

        let userData = JSON.parse(localStorage.getItem('mestria_master_v1')) || { xp: 0, completedLevels: {}, unlocked: { A1: true } };
        let session = { active: false, questions: [], retryQueue: [], index: 0, levelId: null, isRetry: false };

        function showDashboard() {
            document.getElementById('result-screen').classList.add('hidden');
            document.getElementById('quiz-overlay').classList.add('hidden');
            document.getElementById('display-xp').innerText = userData.xp;
            
            const list = document.getElementById('levels-list');
            list.innerHTML = '';
            
            LEVELS.forEach((lvl) => {
                const isUnlocked = userData.unlocked[lvl.id] || false;
                const isDone = userData.completedLevels[lvl.id] || false;
                
                const div = document.createElement('div');
                div.className = `p-6 rounded-3xl border-4 border-black flex items-center gap-6 transition-all ${isUnlocked ? 'bg-white cursor-pointer shadow-[6px_6px_0px_#000000] active:translate-x-1 active:translate-y-1 active:shadow-none' : 'bg-gray-200 opacity-50 cursor-not-allowed'}`;
                
                if (isUnlocked) div.onclick = () => startQuiz(lvl.id);
                
                div.innerHTML = `
                    <div class="w-16 h-16 rounded-2xl flex items-center justify-center text-4xl border-4 border-black ${isUnlocked ? lvl.color : 'bg-gray-400'}">
                        ${isUnlocked ? (isDone ? '✅' : lvl.icon) : '🔒'}
                    </div>
                    <div class="flex-1">
                        <div class="flex justify-between items-center mb-2">
                            <h3 class="font-black text-xl uppercase">${lvl.name}</h3>
                            <span class="font-black text-sm">${isDone ? '40/40' : '0/40'}</span>
                        </div>
                        <div class="w-full bg-gray-200 border-2 border-black h-4 rounded-full overflow-hidden">
                            <div class="bg-blue-500 h-full transition-all duration-700" style="width: ${isDone ? 100 : 0}%"></div>
                        </div>
                    </div>
                `;
                list.appendChild(div);
            });
            localStorage.setItem('mestria_master_v1', JSON.stringify(userData));
        }

        function startQuiz(id) {
            const levelData = MASTER_CURRICULUM[id];
            const rawQs = levelData.data.map((item, idx) => {
                // Maak afleiders (foute antwoorden) uit hetzelfde level
                const distractors = levelData.data
                    .filter(x => x.n !== item.n)
                    .sort(() => 0.5 - Math.random())
                    .slice(0, 3)
                    .map(x => x.n);
                
                const allOptions = [item.n, ...distractors].sort(() => 0.5 - Math.random());
                
                return {
                    id: `${id}-${idx}`,
                    q: `Vertaal: "${item.p}"`,
                    a: allOptions,
                    c: allOptions.indexOf(item.n),
                    cat: levelData.cat
                };
            });

            session = { 
                active: true, 
                questions: rawQs.sort(() => 0.5 - Math.random()), 
                retryQueue: [], 
                index: 0, 
                levelId: id, 
                isRetry: false 
            };
            document.getElementById('quiz-overlay').classList.remove('hidden');
            renderQuestion();
        }

        function renderQuestion() {
            const q = session.isRetry ? session.retryQueue[session.index] : session.questions[session.index];
            document.getElementById('retry-label').classList.toggle('hidden', !session.isRetry);
            document.getElementById('q-category').innerText = q.cat;
            document.getElementById('q-text').innerText = q.q;
            
            const count = session.isRetry ? (40 - session.retryQueue.length + 1) : (session.index + 1);
            document.getElementById('quiz-counter').innerText = `${Math.min(count, 40)}/40`;
            document.getElementById('progress-bar').style.width = `${(count / 40) * 100}%`;

            const container = document.getElementById('options-container');
            container.innerHTML = '';
            
            q.a.forEach((opt, i) => {
                const btn = document.createElement('button');
                btn.className = "option-card w-full p-5 rounded-2xl font-black text-lg text-left leading-tight";
                btn.innerText = opt;
                btn.onclick = () => {
                    const allBtns = document.querySelectorAll('.option-card');
                    allBtns.forEach(b => b.onclick = null);
                    
                    if (i === q.c) {
                        btn.classList.add('correct');
                        setTimeout(() => nextStep(true), 600);
                    } else {
                        btn.classList.add('wrong');
                        allBtns[q.c].classList.add('correct');
                        if (!session.retryQueue.find(x => x.id === q.id)) {
                            session.retryQueue.push(q);
                        }
                        setTimeout(() => nextStep(false), 1200);
                    }
                };
                container.appendChild(btn);
            });
        }

        function nextStep(wasCorrect) {
            if (session.isRetry) {
                if (wasCorrect) {
                    session.retryQueue.splice(session.index, 1);
                } else {
                    session.index++;
                }
                
                if (session.retryQueue.length === 0) return finishQuiz();
                if (session.index >= session.retryQueue.length) session.index = 0;
            } else {
                session.index++;
                if (session.index >= 40) {
                    if (session.retryQueue.length > 0) {
                        session.isRetry = true;
                        session.index = 0;
                    } else {
                        return finishQuiz();
                    }
                }
            }
            renderQuestion();
        }

        function finishQuiz() {
            if (!userData.completedLevels[session.levelId]) {
                userData.xp += 100;
                userData.completedLevels[session.levelId] = true;
                const currentIdx = LEVELS.findIndex(l => l.id === session.levelId);
                if (LEVELS[currentIdx + 1]) {
                    userData.unlocked[LEVELS[currentIdx + 1].id] = true;
                }
            }
            document.getElementById('quiz-overlay').classList.add('hidden');
            document.getElementById('result-screen').classList.remove('hidden');
        }

        function closeQuiz() {
            if (confirm("Wil je de quiz verlaten? Je voortgang gaat verloren.")) {
                document.getElementById('quiz-overlay').classList.add('hidden');
            }
        }

        function sendEmail() {
            const name = document.getElementById('contact-name').value;
            const msg = document.getElementById('contact-msg').value;
            if (!name || !msg) return alert("Vul a.u.b. alle velden in.");
            window.location.href = `mailto:plettingrobin@gmail.com?subject=Mestria Pro Feedback&body=Naam: ${name}%0A%0ABericht: ${msg}`;
        }

        // Init
        showDashboard();
    </script>
</body>
</html>

