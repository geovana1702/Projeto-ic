# Projeto-ic
#include <Arduino.h>
#include <LiquidCrystal_I2C.h>
// ===============================
// Projeto Cinborgue
//Por:
//Edson Oliveira
//Gabriel Coelho Crispim
//Geovana Perereira
//Maria José Barbosa
//Vyvian Hiara Patricio de Freitas 
// ===============================

// Constantes dos Botões
#define BOT_Esquerdo 0
#define BOT_Direito 1
#define BOT_Repete 2
#define BOT_Pula 3
#define BOT_Select 4
#define BUZZER 5

//Botões
#define PIN_Esquerdo A1
#define PIN_Repete   A2
#define PIN_Pula     A3
#define PIN_Direito  A4
#define PIN_Select   A5

// Constantes da lógica do jogo

String Vencedores[3] = {"Indisponivel","","Indisponivel"};
#define Intervalo_Perguntas 4000
#define WIN_SCORE 10
LiquidCrystal_I2C lcd(0x27, 20, 4);
// 

#define COLS 4
#define STR_LEN 5
String area[2]{"Hardware", "Software"};

String Cursos[4]{"EC","CC","SI","IA"};
const int P2[15][4][4] = {
  { {3,1,0,0}, {0,1,3,1}, {1,1,1,1}, {0,1,0,3} },
  { {1,0,0,3}, {0,3,1,0}, {2,0,0,0}, {0,0,2,0} },
  { {0,3,0,0}, {3,0,0,0}, {0,0,0,3}, {0,0,3,0} },
  { {0,3,0,0}, {3,0,0,0}, {0,0,3,0}, {0,0,0,3} },
  { {3,0,0,0}, {0,2,1,0}, {0,1,2,0}, {0,0,0,3} },
  { {3,0,0,0}, {0,3,0,0}, {0,0,3,0}, {0,0,0,3} },
  { {2,1,1,1}, {0,0,0,3}, {0,2,2,1}, {2,0,0,0} },
  { {3,0,0,0}, {0,3,0,0}, {0,0,3,0}, {0,0,0,3} },
  { {3,0,0,0}, {0,3,0,0}, {0,0,3,0}, {0,0,0,3} },
  { {3,0,0,0}, {0,1,2,1}, {0,2,1,0}, {0,0,0,2} },
  { {3,0,0,0}, {0,1,2,0}, {0,0,0,3}, {0,2,1,0} },
  { {3,0,0,0}, {0,2,1,0}, {0,1,2,0}, {0,0,0,3} },
  { {3,0,0,0}, {0,3,0,0}, {0,0,3,0}, {0,0,0,3} },
  { {3,0,0,0}, {1,3,1,0}, {0,1,3,0}, {0,0,0,4} },
  { {3,0,0,0}, {0,3,0,0}, {0,0,3,0}, {0,0,0,3} }
};

const char* PerguntaCurso[15][5]{

  {
    "Voce aperta o botao de ligar e da tela preta, o PC morreu. Qual e a sua reacao imediata?",
    "Busco a chave de fenda na hora. Vamos abrir e dar uma olhada.",
    "Foco na entrega: corro direto para o GRAD. O computador morreu, mas o prazo nao mudou.",
    "Hora de pegar o SSD e arranjar outra maquina, vejo isso depois.",
    "Vejo se houve mudanca e pesquiso o padrao na internet para saber o erro."
  },


  {
    "Para impressionar uma crianca com um poder tecnologico, o que voce faria?",
    "Fazer o carrinho dele andar sozinho pela casa desviando das paredes.",
    "Criar um jogo personalizado onde ele e o heroi da historia.",
    "Consertar o controle quebrado do videogame soldando os fios soltos.",
    "Criar um site de campeonatos para ele organizar as partidas com os amigos."
  },

  {
    "Em um jogo de RPG, qual classe combina mais com voce?",
    "O Mago: estuda livros antigos para conjurar magias complexas.",
    "Artifice: constroi engenhocas mecanicas e cria os itens magicos do grupo.",
    "O Oraculo: preve os movimentos do inimigo analisando padroes.",
    "O Comandante: gerencia o ouro do grupo e define a estrategia da missao."
  },

  {
    "Apocalipse zumbi se instaurou na UFPE! Qual e a sua funcao para defender o Cin?",
    "Hackear o sistema de seguranca para bloquear as portas eletronicas.",
    "Montar uma cerca eletrica improvisada com baterias e fios.",
    "Organizar o racionamento de comida e os turnos de vigilia.",
    "Calcular a rota de fuga com maior probabilidade de sucesso."
  },

  {
    "Voce voltou no inicio da internet, qual inovacao voce lancaria primeiro?",
    "O smartphone: o dispositivo que mudou para sempre a humanidade.",
    "World Wide Web: criar os protocolos que permitem que computadores se falem.",
    "O e-commerce: focado na logistica e em como vender qualquer coisa online.",
    "O algoritmo de busca: organizar toda a informacao do mundo."
  },

  {
    "Num jogo de tabuleiro, voce e quem?",
    "Toca nas pecas dos outros so para ver como funcionam.",
    "Aprende e memoriza as regras rapidinho.",
    "Explica o jogo para todo mundo.",
    "Cria novas regras no meio do jogo pra ficar mais divertido."
  },

  {
    "Se voce tivesse um familiar magico de Hogwarts, qual seria?",
    "Um gnomo que conserta qualquer coisa quebrada sozinho.",
    "Um corvo que aprende novos truques e ensina voce.",
    "Um gato que traz os pergaminhos certos na hora certa.",
    "Um rato curioso que desmonta e testa todos os objetos do laboratorio."
  },

  {
    "Quando alguem fala em computacao, voce pensa em:",
    "Placas, chips e sistemas embarcados.",
    "Algoritmos, linguagens e sistemas.",
    "Empresas, usuarios e solucoes.",
    "Redes neurais e dados."
  },

  {
    "Qual e sua madrugada perfeita?",
    "Solda, cheiro de queimado e arrependimento.",
    "Cafe, teoria e sofrimento silencioso.",
    "Reuniao que poderia ser um e-mail (mas as 2h).",
    "GPU gritando igual turbina de aviao."
  },

  {
    "Voce e o prefeito de uma Smart City futurista. Onde gasta mais do orcamento?",
    "Infraestrutura: rede eletrica inteligente e fibra otica.",
    "Central de controle com dados em tempo real da cidade.",
    "Codigo urbano: semaforos que se otimizam sozinhos.",
    "Seguranca: cameras que previnem crimes."
  },

  {
    "E Carnaval e voce decidiu criar a La Ursa 2. Qual o diferencial?",
    "Um esqueleto animatronico com servos e articulacoes.",
    "Cobranca com QR Code integrado a uma API Pix.",
    "Sensores e cameras para escolher quem assustar.",
    "Sintetizador de voz que cria rimas em tempo real."
  },

  {
    "Toda vez que chego em casa a barata da vizinha esta na minha cama. O que voce faz?",
    "Ensino os quatro calculos para acabar com a sanidade dela.",
    "Ensino ela a programar em Java.",
    "Me junto a ela e faco um plano para dominar o mundo.",
    "Peço ajuda pro Gemini."
  },

  {
    "Byte decidiu que sua cama e dele agora. Voce deixara barato?",
    "This is fine. Sem tempo irmao.",
    "Analiso os movimentos dele para criar uma questao de IP.",
    "Chamo o Troia.",
    "Treino o Byte para entender limites."
  },

  {
    "Voce estava jogando ate o computador travar no combate. O que faz?",
    "Uma chave de fenda e parafusos resolvem.",
    "Reinicio o PC e verifico a BIOS.",
    "Organizo a galera para salvar o jogo.",
    "Treino um bot para evitar travamentos."
  },

  {
    "Voce e de uma Alianca Rebelde e precisa destruir um monstro. Qual e sua funcao?",
    "Mecanico: garantir que tudo funcione sob pressao.",
    "Matematica: enviar sequencias logicas.",
    "Proposito: tentar negociar e entender a intencao.",
    "Padrao: analisar audios e decifrar a lingua."
  }
};

// Variaveis
bool Jogo_Executado[3];
bool Iniciado = false; 

//Função ADKeyboard (A0)

int Leitura_Botoes() {

  if (digitalRead(PIN_Esquerdo) == LOW) {   // ESQUERDA
    delay(200);
    return BOT_Esquerdo;
  }

  if (digitalRead(PIN_Repete) == LOW) {     // REPETE
    delay(200);
    return BOT_Repete;
  }

  if (digitalRead(PIN_Pula) == LOW) {       // PULA
    delay(200);
    return BOT_Pula;
  }

  if (digitalRead(PIN_Direito) == LOW) {    // DIREITA
    delay(200);
    return BOT_Direito;
  }

  if (digitalRead(PIN_Select) == LOW) {     // SELECT
    delay(200);
    return BOT_Select;
  }

  return -1; // nenhum botão
}


// Função Reset geral

void Resetar() {
  for (int i = 0; i < 3; i++) Jogo_Executado[i] = false;
}

void PrintTextoC(String texto, int Linha) {
  if (Linha == 0){lcd.clear();} 
  if (texto.length() <= 20) {
    lcd.setCursor(0, Linha);
    lcd.print(texto);
    if (Linha == 3) {delay(750);}
  }
  else {
    String linha1 = texto.substring(0, 20);
    String restante = texto.substring(20, texto.length());
    if (Linha < 3){
      lcd.setCursor(0, Linha);
      lcd.print(linha1);      
      PrintTextoC(restante, Linha + 1);
    }
    else{
      lcd.print(linha1.substring(0, 20 - 3) + "...");
      delay(750);
    }
  }
}


void somPassar() {
  tone(BUZZER, 800, 50);
  delay(60);
  noTone(BUZZER);
}

void somSelecionar() {
  tone(BUZZER, 400, 120); 
  delay(140);

  tone(BUZZER, 600, 180); 
  delay(200);

  noTone(BUZZER);
}


void musicaComemoracao() {
  tone(BUZZER, 262, 300);
  delay(350);

  tone(BUZZER, 330, 300);
  delay(350);

  tone(BUZZER, 392, 300);
  delay(350);

  tone(BUZZER, 523, 600);
  delay(700);

  noTone(BUZZER);
}

void PrintTextoP(String texto0, String texto1, String texto2, String texto3){
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print(texto0);
  lcd.setCursor(0, 1);
  lcd.print(texto1);
  lcd.setCursor(0, 2);
  lcd.print(texto2);
  lcd.setCursor(0, 3);
  lcd.print(texto3);
  delay(750);
}
// Escolha de Alternativa
int EscolhaABCD2(int N){
  int Selecionado = 0;
  int ultimo_Selecionado = 5;  
  PrintTextoC(PerguntaCurso[N][0], 0);
  delay(Intervalo_Perguntas);
  while(true){
    if (Selecionado < 0){Selecionado = 3;}
    else if (Selecionado > 3){Selecionado = 0;}
    if (ultimo_Selecionado != Selecionado){
      PrintTextoC(PerguntaCurso[N][Selecionado + 1], 0);
    }
    ultimo_Selecionado = Selecionado;
    int BOT = Leitura_Botoes();
    if (BOT == -1) {continue;}
    else if (BOT == BOT_Esquerdo) {
      Selecionado = Selecionado - 1;
      somPassar();
    }
    else if (BOT == BOT_Direito) {
      Selecionado = Selecionado + 1;
      somPassar();
    }
    else if (BOT == BOT_Repete){
      PrintTextoC(PerguntaCurso[N][0], 0);
      delay(Intervalo_Perguntas);
      ultimo_Selecionado = 5;
    }
    else if (BOT == BOT_Select) {
      somSelecionar();
      return Selecionado;
    }
  }
}

// Jogo 2: Cursos

void Jogo_Cursos() {
  PrintTextoP("Hora de ver","seu curso ideal","no CIn","");
  delay(Intervalo_Perguntas);
  int Pontuacao[4] = {0, 0, 0, 0};
  int Maior_Pontuacao = 0;
  while (Vencedores[1] == ""){
    int nPergunta = random(0,15);
    int Escolhida = EscolhaABCD2(nPergunta);
    for (int p = 0; p < 4; p++){
      Pontuacao[p] += P2[nPergunta][Escolhida][p];
      Serial.print(P2[nPergunta][Escolhida][p]);
      if (Pontuacao[p] >= 8 && Pontuacao[p] > Maior_Pontuacao){
        Vencedores[1] = Cursos[p];
        Maior_Pontuacao = Pontuacao[p];
      }
    }
  }
  PrintTextoC("Temos seu Resultado", 0);
  delay(2500);
  PrintTextoP("Temos seu Resultado","Seu Curso Ideal e...","","");
  delay(2500);
  musicaComemoracao();
  PrintTextoP("Temos seu Resultado","Seu Curso Ideal e...",Vencedores[1],"");
  delay(2500);
}

void menu() {
  int Opcao = 0;
  bool mudanca = true;
  static int ultimoBotao = -1;

  while (true) {
    if (Opcao > 3){ Opcao = 0;}
    else if (Opcao < 0){ Opcao = 3;}
    String Menu_Jogos[] = {"HardwareXSoftware","Cursos do Cin","Centros da UFPE","Encerrar"};
    if (mudanca){
      PrintTextoP("","MENU",Menu_Jogos[Opcao],"");
      if (Jogo_Executado[Opcao]){PrintTextoC("Resultado:" + Vencedores[Opcao], 3);}
      mudanca = false;
    }
    int BOT = Leitura_Botoes();
    if (BOT == -1) {continue;}
    else if (BOT == BOT_Esquerdo) {
      Opcao = Opcao - 1;
      somPassar();
      mudanca = true;
    }
    else if (BOT == BOT_Direito) {
      Opcao = Opcao + 1;
      somPassar();
      mudanca = true;
    }
    else if (BOT == BOT_Select) {
      somSelecionar();
      if (Opcao == 3) return;
      if (Opcao == 1 && !Jogo_Executado[1]) {
        Jogo_Executado[1] = true;
        Jogo_Cursos();
        mudanca = true;
      }
      if (Opcao == 2 || Opcao == 0){
        PrintTextoP("Infelizmente por","Limites de Ram","essa opcao nao","esta disponivel");
        Jogo_Executado[0] = true;
        Jogo_Executado[2] = true;
        delay(4000);
        mudanca = true;
      }
      if (Opcao == 2) {
        break;
      }
    }
  }
}
// Setup

void setup() {
  Serial.begin(9600);
  Resetar();
  lcd.init();
  lcd.backlight();
  PrintTextoP("","Iniciar?","","");
  randomSeed(analogRead(A0));
  // Botões
  pinMode(PIN_Esquerdo, INPUT_PULLUP);
  pinMode(PIN_Repete,   INPUT_PULLUP);
  pinMode(PIN_Pula,     INPUT_PULLUP);
  pinMode(PIN_Direito,  INPUT_PULLUP);
  pinMode(PIN_Select,   INPUT_PULLUP);
  pinMode(BUZZER, OUTPUT);
}

// Loop

void loop() {
  bool unica = true;
  if (Leitura_Botoes() != -1){
    if (unica){
      PrintTextoP("Bem Vindo, Eu sou","Cinborgue.Estou aqui","Para te ajudar","Calouro, Vamos la?");
      delay(4000);
      unica = false;
    }      
    menu();
    Iniciado = true;
    }
  if (Iniciado){
    PrintTextoP("Obrigado por Jogar!","Boa Sorte","na sua jornada","academica");
    delay(3000);
    Iniciado = false;
    Resetar();
    PrintTextoP("","Iniciar?","","");
  }
}
