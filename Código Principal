from abc import ABC, abstractmethod
import random
import pickle

class UnidadeNaval(ABC):
    @abstractmethod
    def __init__(self, tamanho):
        self.tamanho = tamanho
        self.atingido = [False] * tamanho

    @abstractmethod
    def get_tipo(self):
        pass

    @abstractmethod
    def atingir(self, indice):
        pass

    @abstractmethod
    def esta_destruido(self):
        pass

# Definindo uma classe abstrata para representar unidades navais

class Navio(UnidadeNaval):
    def __init__(self):
        super().__init__(3)

    def get_tipo(self):
        return "N"

    def atingir(self, indice):
        # Certificando-se de que o índice está dentro dos limites
        if 0 <= indice < len(self.atingido):
            self.atingido[indice] = True
        else:
            print('Índice fora dos limites.')

    def esta_destruido(self):
        return all(self.atingido)

# Implementação da classe Navio, que herda de UnidadeNaval

class Tabuleiro:
    def __init__(self):
        self.tabuleiro = [['O' for _ in range(5)] for _ in range(5)]

    def imprimir(self, ocultar_navios):
        # Imprimindo o tabuleiro, ocultando navios se necessário
        for linha in self.tabuleiro:
            print(' '.join(['O' if cell == 'N' and ocultar_navios else cell for cell in linha]))

    def posicionar_navios(self, quantidade, tipo_unidade):
        # Posicionando navios no tabuleiro de acordo com a orientação
        for _ in range(quantidade):
            while True:
                orientacao = random.choice(['horizontal', 'vertical'])
                if orientacao == 'horizontal':
                    x = random.randint(0, 5 - tipo_unidade.tamanho)
                    y = random.randint(0, 4)
                    if all(self.tabuleiro[y][x+i] == 'O' for i in range(tipo_unidade.tamanho)):
                        for i in range(tipo_unidade.tamanho):
                            self.tabuleiro[y][x+i] = tipo_unidade.get_tipo()[0]
                        break
                else:
                    x = random.randint(0, 4)
                    y = random.randint(0, 5 - tipo_unidade.tamanho)
                    if all(self.tabuleiro[y+i][x] == 'O' for i in range(tipo_unidade.tamanho)):
                        for i in range(tipo_unidade.tamanho):
                            self.tabuleiro[y+i][x] = tipo_unidade.get_tipo()[0]
                        break

    def salvar_jogo(self, nome_arquivo):
        # Salvando o tabuleiro em um arquivo pickle
        with open('{}.pkl'.format(nome_arquivo), 'wb') as arquivo:
            pickle.dump(self.tabuleiro, arquivo, protocol=pickle.HIGHEST_PROTOCOL)

    def carregar_jogo(self, nome_arquivo):
        # Carregando o tabuleiro de um arquivo pickle
        with open('{}.pkl'.format(nome_arquivo), 'rb') as arquivo:
            saved_state = pickle.load(arquivo)
            
            # Verificando se o tabuleiro já contém dados antes de carregar o jogo
            if any(cell != 'O' for linha in self.tabuleiro for cell in linha):
                print("Aviso: O tabuleiro já contém dados. Não foi possível carregar o jogo.")
                return None

            # Substituindo o tabuleiro pelo tabuleiro carregado
            self.tabuleiro = saved_state
            return self.tabuleiro

# Implementação da classe Tabuleiro, responsável pela manipulação do tabuleiro de jogo

class JogoBatalhaNaval:
    def __init__(self):
        self.tabuleiro_jogador = Tabuleiro()
        self.tabuleiro_computador = Tabuleiro()
        self.tipo_unidade_jogador = Navio()
        self.tipo_unidade_computador = Navio()
        
    def menu_principal(self):
        # Menu principal do jogo com opções para jogar, carregar jogo ou sair
        while True:
            print("\n===== Menu Principal =====")
            print("1. Jogar")
            print("2. Carregar Jogo")
            print("3. Sair")

            escolha = input("Escolha uma opção (1-3): ")

            if escolha == '1':
                self.jogar()
            elif escolha == '2':
                nome_arquivo = input("Digite o nome do arquivo para carregar o jogo: ")
                self.tabuleiro_jogador.carregar_jogo(nome_arquivo)
                self.tabuleiro_computador.carregar_jogo(nome_arquivo)
                print(f"Jogo carregado de {nome_arquivo}")
                self.jogar()
            elif escolha == '3':
                print("Saindo do jogo.")
                break
            else:
                print("Opção inválida. Tente novamente.")
    
    def jogar(self):
        # Lógica principal do jogo
        nome_arquivo = None  # Nome do arquivo ainda não fornecido
        coordenadas_escolhidas_jogador = [] # Coordenadas já jogadas
        print("Bem-vindo ao jogo de Batalha Naval!")

        self.tabuleiro_jogador.posicionar_navios(3, self.tipo_unidade_jogador)
        self.tabuleiro_computador.posicionar_navios(3, self.tipo_unidade_computador)

        while True:
            print("\nTabuleiro do Jogador:")
            self.tabuleiro_jogador.imprimir(ocultar_navios=False)
            print("\nTabuleiro do Computador:")
            self.tabuleiro_computador.imprimir(ocultar_navios=True)

            acao = input("Digite 's' para salvar, 'm' para voltar ao menu ou pressione Enter para continuar: ")

            if acao.lower() == 's':
                if nome_arquivo is None:
                    nome_arquivo = input("Digite o nome do arquivo para salvar o jogo: ")
                self.tabuleiro_jogador.salvar_jogo(nome_arquivo)
                self.tabuleiro_computador.salvar_jogo(nome_arquivo)
                print(f"Jogo salvo como {nome_arquivo}")
                
            elif acao.lower() == 'm':
                    print("Voltando ao menu principal.")
                    return

            while True:
                x = int(input("Digite a coordenada X para o seu disparo (0-4): "))
                y = int(input("Digite a coordenada Y para o seu disparo (0-4): "))

                if (x, y) in coordenadas_escolhidas_jogador:
                    print("Você já escolheu essas coordenadas. Por favor, escolha novas coordenadas.")
                elif x < 0 or x > 4 or y < 0 or y > 4 or x == '' or y == '':
                    print("Coordenadas inválidas. Tente novamente.")
                else:
                    coordenadas_escolhidas_jogador.append((x, y))
                    break
            
            self.realizar_disparo(self.tabuleiro_computador, x, y)
            print("\nVez do Computador:")
            x_computador = random.randint(0, 4)
            y_computador = random.randint(0, 4)
            self.realizar_disparo(self.tabuleiro_jogador, x_computador, y_computador)
                
            if self.verificar_fim_de_jogo():
                break

        if self.tipo_unidade_jogador.esta_destruido():
            print("Parabéns! Você venceu!")
        else:
            print("Você perdeu. Tente novamente.")

    def realizar_disparo(self, tabuleiro, x, y):
        # Lógica para realizar um disparo no tabuleiro
        tipo_unidade = self.tipo_unidade_jogador if tabuleiro is self.tabuleiro_computador else self.tipo_unidade_computador

        if tabuleiro.tabuleiro[y][x] == tipo_unidade.get_tipo():
            print("Acertou!")
            tabuleiro.tabuleiro[y][x] = 'X'
            tipo_unidade.atingir(x)
            return True
        else:
            print("Errou!")
            tabuleiro.tabuleiro[y][x] = '-'
            return False

    def verificar_fim_de_jogo(self):
        # Verifica se todos os navios do jogador foram destruídos
        jogador_destruido = self.tipo_unidade_jogador.esta_destruido() and all(
            cell in ['X', '-'] for linha in self.tabuleiro_jogador.tabuleiro for cell in linha
        )

        # Verifica se todos os navios do computador foram destruídos
        computador_destruido = self.tipo_unidade_computador.esta_destruido() and all(
            cell in ['X', '-'] for linha in self.tabuleiro_computador.tabuleiro for cell in linha
        )

        # Verifica se houve 9 'X' em algum dos tabuleiros
        x_jogador = sum(row.count('X') for row in self.tabuleiro_jogador.tabuleiro)
        x_computador = sum(row.count('X') for row in self.tabuleiro_computador.tabuleiro)

        # Retorna True se qualquer uma das condições for atendida
        return jogador_destruido or computador_destruido or x_jogador == 9 or x_computador == 9


# Implementação da classe principal do jogo

if __name__ == "__main__":
    jogo = JogoBatalhaNaval()
    jogo.menu_principal()
