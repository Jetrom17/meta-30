![](https://private-user-images.githubusercontent.com/72875404/379323342-bee43da5-ee8f-4d47-b7d5-29fd24d65755.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3Mjk2OTY0NzksIm5iZiI6MTcyOTY5NjE3OSwicGF0aCI6Ii83Mjg3NTQwNC8zNzkzMjMzNDItYmVlNDNkYTUtZWU4Zi00ZDQ3LWI3ZDUtMjlmZDI0ZDY1NzU1LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDEwMjMlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQxMDIzVDE1MDkzOVomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTFjMDA3NjY3M2JlNWU3M2YxYTljYTgzNzZmMWUyMGMyOGE4YjYxOTYwYjcwNzJjZTQyNzcyY2UwYjc2ZGNjZjEmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.NZKU7iRAJm91_73eNx5uuJp4zlnOEsUUbWSyg6WeYfg)

Você pensou em uma meta, mas nunca alcançou? Esse problema é comum, mas não impossível. Segundo uma notícia da [Setcsp](https://setcesp.org.br/noticias/revisitando-a-preguica/#:~:text=Do%20ponto%20de%20vista%20da,de%20emerg%C3%AAncia%20ou%20necessidades%20futuras.) o cérebro tende economizar energia, de forma a preservar recursos para situações de emergência. 

Em um blog de [renatoalves](https://renatoalves.com.br/blog/exercicios-vencer-preguica-mental/#:~:text=A%20pregui%C3%A7a%20mental%20pode%20nos,de%20deixar%20tudo%20para%20depois.) existem 15 ou mais exercícios para driblar a preguiça mental.

Pensando nisso eu desenvolvi um site do tipo [PWA](https://developer.mozilla.org/pt-BR/docs/Web/Progressive_web_apps) para ajudar pessoas a cumprir sua meta. Você pode acessar aqui e já definir sua meta diária em 30 dias. Além de recomendar para outras pessoas.
> https://jetrom17.github.io/meta-30/

Eu adicionei para cada dia que usuário marcar, apareça uma mensagem bíblica. Uma forma de promover a pessoa cumprir sua meta com motivação.

```js
const biblicQuotes = [
            "Não se turbe o vosso coração; credes em Deus, crede também em mim. - João 14:1",
            "Posso todas as coisas naquele que me fortalece. - Filipenses 4:13",
            "O Senhor é o meu pastor; nada me faltará. - Salmo 23:1",
            "Entrega o teu caminho ao Senhor; confia nele, e o mais ele fará. - Salmo 37:5",
            "Não andeis ansiosos de coisa alguma; antes, em tudo, sejam conhecidos diante de Deus os vossos pedidos. - Filipenses 4:6",
[...]
```

Adicionado PWA, já dito anteriomente, porém não explicado. Configurei para ficar offline, logo no primeiro momento que você visita o site, óbvio. 

```js
const CACHE_NAME = 'meta-30-cache-v1';
const urlsToCache = [
    '/',
    '/index.html',
    '/manifest.json',
    '/icon.png',
];

// Instalando o service worker e armazenando em cache os recursos
self.addEventListener('install', (event) => {
    event.waitUntil(
        caches.open(CACHE_NAME)
            .then((cache) => {
                return cache.addAll(urlsToCache);
            })
    );
});
```

No próprio arquivo `index.html` inserir no CSS o elemento `:root`. É vantajoso porque permite definir variáveis, isto é, isso facilita a manutenção e a consistência do estilo em todo documento do site carregado. `root = raiz` (Escopo Global).

```css
 :root {
            --md-sys-color-primary: #2E7D32; 
            --md-sys-color-on-primary: #FFFFFF; 
            --md-sys-color-surface: #FFFFFF; 
            --md-sys-color-on-surface: #1B5E20; 
            --md-sys-color-background: #E8F5E9;
            --md-sys-color-secondary: #66BB6A;
        }
```

Toda ação do usuário é salva no `localStorage`, isto é, no seu navegador. Primeiro você digita a meta que irá cumprir durante 30 dias, seja programação, rotina ou algo específico neste período. Dito isso você dar `enter` ou se for em smartphone, clica com dedo fora do `input`.

```js
metaInput.addEventListener('input', function () {
            const meta = metaInput.value;
            if (meta.length <= 15) {
                metaTitle.innerText = `Meta ${meta}`;
                localStorage.setItem('meta', meta);
            }
        });
```

Claro que se for seu primeira vez ao acessar o site, você terá um `BottomSheet` de boas vindas que irá registrar no seu nevegador para que não apareça novamente a mensagem.

```js
// Verificar se é a primeira vez que está acessando
if (!localStorage.getItem('bottomSheetShown')) {
    showBottomSheet("Bem-vindo ao Meta-30! Defina suas metas e comece a sua jornada."); // Mensagem padrão
    localStorage.setItem('bottomSheetShown', 'true');
}
```

No final, você terá confetes. Dizendo para você mesmo, que conseguiu de fato cumprir. Provável que se tenha [dopamina](https://www.tuasaude.com/dopamina/#:~:text=A%20dopamina%20%C3%A9%20um%20neurotransmissor,%2C%20peixe%2C%20carnes%20ou%20feij%C3%A3o.), o que é muito bom neste caso.

```js
function lançarConfetes() {
            var duration = 3 * 1000;
            var end = Date.now() + duration;

            (function frame() {
                confetti({
                    particleCount: 5,
                    angle: 60,
                    spread: 55,
                    origin: { x: 0 }
                });
                confetti({
                    particleCount: 5,
                    angle: 120,
                    spread: 55,
                    origin: { x: 1 }
                });

                if (Date.now() < end) {
                    requestAnimationFrame(frame);
                }
            }());
        }
```

Se não tem nehuma meta em mente, então eu sugiro: Meta **sem porn**.

![](https://i.imgur.com/pNuBHFI.jpeg)

Por quê?

Pronografia segundo vários sites, como Ghow, [Senado](https://www12.senado.leg.br/radio/1/noticia/2023/07/13/cas-debate-o-vicio-em-pornografia-e-consequencias-entre-jovens#:~:text=Os%20cientistas%20est%C3%A3o%20divididos%3A%20alguns,compuls%C3%A3o%20e%20a%20preju%C3%ADzos%20financeiros.) e outros, cientistas estão divididos: alguns concordam que a pornografia pode causar danos psicológicos e até físicos. Também existem críticas de que o hábito pode arruinar casamentos e causar desvios comportamentais, levando a uma compulsão e a prejuízos financeiros.

#

O objetivo é ajudar você a alcançar uma meta em 30 dias. Você define essa meta uma vez, evitando tarefas diárias, o que permite focar em um único alvo.

Por exemplo, se você quer criar um aplicativo em Java, no primeiro dia, você prepara o ambiente. No segundo dia, continua o desenvolvimento, mantendo o foco. Ao final de 30 dias, você terá internalizado a importância do seu objetivo.

A clareza do conteúdo é importante, e a inclusão de versículos bíblicos pode não ressoar com todos. O código fonte está disponível para que você possa personalizar as frases como preferir.

> [!Note]
> Para melhor experiência use no seu smartphone.
