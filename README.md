# Portfólio 3D: MacBook desmontável

Página de portfólio em que um MacBook Pro 3D se desmonta conforme a rolagem, e a tela do notebook mostra cada projeto (feito por Genesis Melo). Three.js, sem build: é só abrir `index.html` por um servidor estático.

Demo: https://7genesis.github.io/portfolio-3d-macbook/

## O que mostra

A rolagem percorre 8 capítulos: Perfil, Por dentro, LeadFlow, SAAE, Sites, Dados, Carreira e Contato. O notebook abre, mostra a placa-mãe e a bateria por dentro, e a tela exibe as capturas dos projetos; no capítulo do SAAE entra um iPhone 3D. A barra inferior indica o progresso e os pontos de navegação levam direto a cada capítulo.

## Decisões técnicas

- **Sem build.** São três arquivos: `index.html` (cena, estilos e textos), `assets.js` (capturas de tela embutidas) e os modelos em `modelos3d/`. Three.js 0.160.0 vem de um CDN por `importmap`, sem `npm` e sem bundler. Motivo: é uma página única de demonstração, e o deploy é só servir arquivos estáticos (GitHub Pages).
- **Rolagem nativa dirigindo a cena.** O `body` ganha uma altura proporcional ao tempo de cada capítulo, e a posição de rolagem vira um progresso de 0 a 1. A animação segue esse valor com amortecimento exponencial (`damp`), então o movimento fica suave e igual em telas de 60 Hz e de 120 Hz. A rolagem do navegador nunca é bloqueada nem substituída, e os pontos de navegação usam `scrollTo` suave.
- **Cada capítulo tem uma parada e uma transição.** A tabela de tempos de parada define quanta rolagem cada capítulo ocupa. Nas paradas o conteúdo fica estável e as capturas passam em sequência; nas transições a câmera, a luz de acento e o desmonte do notebook interpolam entre capítulos.
- **Notebook desmontado por agrupamento.** As malhas do modelo são separadas em tampa e base (pela altura de cada malha) e movidas por grupos, o que permite abrir e afastar as partes sem editar o modelo. A placa-mãe, os chips, a bateria, as ventoinhas e um tubo de cobre são geometrias desenhadas em código.
- **Telas como textura de canvas.** O conteúdo da tela do MacBook e do iPhone é desenhado em `CanvasTexture` e trocado com a rolagem, em vez de vídeo. O material da tela é `MeshBasicMaterial`, sem influência da iluminação.
- **Aparência.** `RoomEnvironment` para reflexos, tone mapping ACES Filmic, sombras suaves e um pass de bloom (`UnrealBloomPass`) de intensidade baixa.
- **Modelos otimizados.** Os `.glb` foram comprimidos com Meshopt (`MeshoptDecoder`) e ficam em 2,1 MB (MacBook) e 2,8 MB (iPhone); uma barra de carregamento acompanha o download.
- **Custo e acessibilidade.** O `devicePixelRatio` é limitado a 1,75 (1,25 em tela estreita), o render para quando a aba fica oculta e com `prefers-reduced-motion` a entrada animada, a flutuação do notebook e o suavizado da rolagem são desligados. Abaixo de 860 px de largura a câmera e o enquadramento mudam para a tela estreita.

## Rodar

```bash
python3 -m http.server 8777
# abra http://127.0.0.1:8777
```

Precisa de internet para carregar o Three.js (unpkg) e as fontes (Google Fonts). Abra por um servidor local, não direto pelo arquivo.

Testes: o projeto não tem testes automatizados.

## Estrutura

```
index.html     cena Three.js, estilos e textos dos capítulos
assets.js      capturas de tela dos projetos (embutidas)
modelos3d/     macbook_pro_m3_16_inch_2024_c.glb, iphone_17_pro_c.glb
```

## Modelos 3D (CC BY 4.0)

- **MacBook Pro M3 16 inch 2024**, de [jackbaeten](https://sketchfab.com/jackbaeten): https://sketchfab.com/3d-models/macbook-pro-m3-16-inch-2024-8e34fc2b303144f78490007d91ff57c4
- **iPhone 17 Pro**, de [Ranguel](https://sketchfab.com/Ranguel): https://sketchfab.com/3d-models/iphone-17-pro-4541aa8a28324b33a2baaf81d263aaec

Licença: [Creative Commons Atribuição 4.0](http://creativecommons.org/licenses/by/4.0/). Alterações: compressão e otimização das malhas (`*_c.glb`), troca de material da tela por uma textura animada. Apple, MacBook e iPhone são marcas da Apple Inc.; o projeto não tem vínculo com a Apple.

## Imagens

As capturas de tela em `assets.js` são de projetos públicos do autor (StockFlow, CreditFlow, LeadFlow Engine e landing pages entregues a clientes).
