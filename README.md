Something Is Wrong

Mod de terror psicológico / ARG para Minecraft Java 1.21.1 (Fabric, Java 21). Toda a experiência acontece dentro da janela do Minecraft: sem rede, sem comandos do sistema, sem fechar o jogo, sem tocar em arquivos fora do mod.

Novidades da v1.2

- Watcher evolutivo (4 fases, textura própria em cada uma): fica visível por mais tempo, se aproxima mais rápido e, nas fases altas, "pisca" para o lado de forma imprevisível. Nunca causa dano e nunca chega a menos de ~5 blocos.
- Medo invisível: sem barra na tela. O que você presencia só ajusta a frequência dos eventos (0,85x a 1,45x).
- Luneta: olhar o Watcher pela luneta faz ele "olhar de volta" por 1,5 s antes de sumir.
- Watcher responde ao chat (PT/EN): "quem é você", "socorro", "oi", "vou embora"... e uma frase secreta.
- 3 finais (ignorou / olhou muito / conversou com ele) e 17 conquistas ocultas.
- 25 anomalias: ilusões visuais só para você (pilar escuro que não existe no mundo), cinzas caindo, o Imitador (repete seus passos, abre um baú que não existe).
- 3 itens: Diário Rasgado (relê o lore já ouvido), Olho de Vidro (detector: contorno do Watcher por 5 s, receita: 4 painéis de vidro + olho do Ender) e Amuleto do Silêncio (reduz o medo; receita sem forma: fragmento de ametista + linha + corante preto). Aba própria no inventário criativo.
- Compatibilidade com outros mods: sem mixins e sem alterar nada do jogo base; usa registros e tags padrão. Tag "somethingiswrong:hissable" (blocos que "chiam"; datapacks e mods podem acrescentar), lista "blockedDimensions" na config para desligar o mod em dimensões de outros mods, e as anomalias reconhecem blocos, animais e monstros adicionados por outros mods. Não testei com mods específicos.
- Configuração dentro do jogo: "/somethingiswrong config ..." (operador, ou o dono do mundo em single player).

Status honesto desta entrega

Verificação| Resultado
Lógica pura compilada com "javac" 21 e 42 testes executados (runner mínimo; o JUnit real roda no "gradle build")| 42/42 passaram
Recursos referenciados (42 falas narradas, 10 sons, texturas, 17 conquistas, itens, receitas, traduções) conferidos por script| OK
Sintaxe das classes Fabric/Minecraft| sem erros; a v1.0 compilou no GitHub Actions. A v1.2 ainda não foi compilada contra o Minecraft

Compilar

Requisitos: JDK 21.

gradle wrapper --gradle-version 8.10.2   # uma vez (gera gradlew + jar do wrapper)
./gradlew build                          # compila, roda os testes JUnit e gera o JAR

JAR final: "build/libs/something-is-wrong-1.0.0.jar" (gerado pela tarefa "remapJar"/"build").

Cliente de desenvolvimento: "./gradlew runClient".

Se uma versão do "gradle.properties" não resolver, atualize em https://fabricmc.net/develop/ (1.21.1).

Instalar

1. Instale o Fabric Loader (>= 0.16.0) para 1.21.1 e o Fabric API.
2. Copie "something-is-wrong-1.0.0.jar" para ".minecraft/mods/".
3. Abra ou crie um mundo. A campanha começa sozinha na primeira entrada ("autoStart"). Nada acontece nos primeiros 3 minutos.

O mod precisa estar no cliente e no servidor (entidade e sons próprios).

Como funciona

- Relógio em ticks do jogo, persistido no mundo ("data/somethingiswrong_campaign.dat"). Pausa, desconexão e reinício não causam saltos; o relógio só corre com jogador ativo. Duração configurável (padrão 20 min).
- Fases: 0-3 normalidade · 3-6 coincidências · 6-9 algo errado · 9-12 presença · 12-15 entidade · 15-18 ruptura · 18-20 clímax; aos 20:00 inicia o final programado (~25 s).
- Anomalias (10): "footsteps_unseen", "distant_silhouette", "odd_direction_sound", "lone_message", "watcher_flicker", "watcher_vanish_on_look", "close_ambient_noise", "visual_interference", "behavior_adaptive", "final_sequence". Cada uma tem janela, chance, cooldown, limite de repetições, requisitos e cancelamento (jogador em combate/lava/água/fogo/queda/pouca vida cancela efeitos pendentes).
- The Watcher: invulnerável, sem drops/XP, sem ataque. Observação checada a cada 5 ticks (distância, cone de 28°, linha de visão por raycast). Some com fumaça ao ser olhado. Só uma instância por vez; instâncias órfãs (ex.: restauradas do disco) se removem sozinhas.
- Final: lentidão (máx. II, ≤ 400 ticks), cegueira (≤ 100 ticks), interferência suave, mensagem final e tela de encerramento. Todos os efeitos são finitos e removidos ao terminar. Nunca repete (estado "FINISHED").
- Interferência visual: só na camada de HUD, com fade (sem estroboscópio); abrir qualquer tela (ESC) a esconde.

Configuração ("config/somethingiswrong.json")

"enabled", "intensity" (0-100), "eventFrequency" (0.1-3), "visualInterference", "customSounds", "campaignMinutes" (3-60), "debug", "narrationLanguage" ("pt"/"en"/"off"), "narrationText", "chatResponses", "blockedDimensions", "autoStart", "resetCampaignOnNextStart" (reinicia só a campanha, uma vez, e volta a "false"), "observationMinDistance", "observationMaxDistance", "anomalyChance" (multiplicadores 0-2 por id).

Valores inválidos são corrigidos só em memória; um arquivo ilegível nunca é sobrescrito.

Depuração (operador, nível 2)

/somethingiswrong status | time | start | events | reload
/somethingiswrong phase next
/somethingiswrong trigger <id>            (execução forçada; não conta no registro)
/somethingiswrong watcher spawn | remove
/somethingiswrong climax start            (testa o final sem esperar 20 min)
/somethingiswrong reset confirm           (reinicia SÓ o estado da campanha; o mundo não é tocado)

Limitações conhecidas

- Não compilado contra o Minecraft real neste ambiente. APIs usadas com nomes Yarn 1.21.1 (ex.: "PersistentState.Type", "FabricEntityTypeBuilder", "PlaySoundS2CPacket", "HudRenderCallback" com "RenderTickCounter"); ajustes pontuais de assinatura podem ser necessários.
- Sem mixins (nenhum foi necessário).
- Texturas e sons são gerados proceduralmente (originais, simples). Substitua por assets melhores em "assets/somethingiswrong".
- A silhueta distante usa a própria entidade a partir de 6:00, a >38 blocos; a apresentação formal vem aos 9-12 min.
- Multijogador: eventos miram um jogador por vez; o final afeta todos os jogadores ativos.
- Os efeitos atrasados (passos em sequência) não persistem entre reinícios (são cosméticos).
- Testes de integração no jogo (spawn real, render, som) ficam pendentes: execute "./gradlew runClient" e use os comandos acima.

---

❤️ Apoie o Something Is Wrong

Gostou do Something Is Wrong? Você pode apoiar voluntariamente o desenvolvimento deste mod independente de terror para Minecraft Java.

Sua contribuição ajuda a manter o projeto e desenvolver futuras atualizações, novas anomalias, eventos assustadores e melhorias para o Watcher.

📱 Apoio via Pix

- Contribuição: totalmente voluntária.
- Valor: escolhido pelo apoiador.
- Obrigatório para jogar? Não. O mod continua gratuito.
- Benefícios exclusivos: nenhuma vantagem é prometida em troca do apoio.

🔑 Como apoiar

As instruções de pagamento e o QR Code Pix serão adicionados após a verificação dos dados de recebimento.

Antes de confirmar qualquer pagamento, confira o nome do destinatário e o valor no aplicativo do seu banco.

❤️ Obrigado por apoiar o desenvolvimento do Something Is Wrong e fazer parte da comunidade!

Projeto independente, sem vínculo oficial com a Mojang ou a Microsoft.