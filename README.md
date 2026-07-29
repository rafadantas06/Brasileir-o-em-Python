import random

times = [
    "Corinthians", "Palmeiras", "Flamengo", "São Paulo",
    "Santos", "Grêmio", "Internacional", "Cruzeiro",
    "Atlético-MG", "Fluminense", "Athletico-PR", "RB Bragantino",
    "Botafogo", "Coritiba", "Vitória", "Vasco", "Remo", "Mirassol",
    "Chapecoense", "Bahia"
]

tabela = {}

for time in times:
    tabela[time] = {
        "P": 0,    
        "J": 0,    
        "V": 0,   
        "E": 0,    
        "D": 0,    
        "GP": 0,   
        "GC": 0,   
        "SG": 0    
    }

historico = []


def registrar_partida(casa, gols_casa, visitante, gols_visitante):
    tabela[casa]["J"] += 1
    tabela[visitante]["J"] += 1

    tabela[casa]["GP"] += gols_casa
    tabela[casa]["GC"] += gols_visitante

    tabela[visitante]["GP"] += gols_visitante
    tabela[visitante]["GC"] += gols_casa

    if gols_casa > gols_visitante:
        tabela[casa]["V"] += 1
        tabela[casa]["P"] += 3

        tabela[visitante]["D"] += 1

    elif gols_casa < gols_visitante:
        tabela[visitante]["V"] += 1
        tabela[visitante]["P"] += 3

        tabela[casa]["D"] += 1

    else:
        tabela[casa]["E"] += 1
        tabela[visitante]["E"] += 1

        tabela[casa]["P"] += 1
        tabela[visitante]["P"] += 1

    tabela[casa]["SG"] = tabela[casa]["GP"] - tabela[casa]["GC"]
    tabela[visitante]["SG"] = tabela[visitante]["GP"] - tabela[visitante]["GC"]

    historico.append({
        "casa": casa,
        "visitante": visitante,
        "gols_casa": gols_casa,
        "gols_visitante": gols_visitante
    })


def zona_classificacao(posicao, total_times):
    if posicao <= 4:
        return "Libertadores"
    elif posicao <= 6:
        return "Pré-Libertadores"
    elif posicao <= 12:
        return "Sul-Americana"
    elif posicao <= total_times - 4:
        return " "
    else:
        return "Rebaixamento"


def simular_rodada():
    times_embaralhados = times[:]
    random.shuffle(times_embaralhados)

    print("Rodada simulada:")
    for i in range(0, len(times_embaralhados) - 1, 2):
        casa = times_embaralhados[i]
        visitante = times_embaralhados[i + 1]
        gols_casa = random.randint(0, 5)
        gols_visitante = random.randint(0, 5)

        registrar_partida(casa, gols_casa, visitante, gols_visitante)
        print(f"{casa} {gols_casa} x {gols_visitante} {visitante}")

    if len(times_embaralhados) % 2 != 0:
        print(f"{times_embaralhados[-1]} Não jogou na rodada.")


def mostrar_estatisticas_time(time):
    dados = tabela[time]

    if dados["J"] == 0:
        print(f"{time} ainda não jogou nenhuma partida.")
        return

    aproveitamento = (dados["P"] / (dados["J"] * 3)) * 100
    media_gp = dados["GP"] / dados["J"]
    media_gc = dados["GC"] / dados["J"]

    print(f"===== ESTATÍSTICAS - {time} =====")
    print(f"Jogos: {dados['J']}  |  Vitórias: {dados['V']}  |  Empates: {dados['E']}  |  Derrotas: {dados['D']}")
    print(f"Gols marcados: {dados['GP']} (média de {media_gp:.2f} por jogo)")
    print(f"Gols sofridos: {dados['GC']} (média de {media_gc:.2f} por jogo)")
    print(f"Saldo de gols: {dados['SG']}")
    print(f"Aproveitamento: {aproveitamento:.1f}%")


while True:

    print("=== SIMULADOR DO BRASILEIRÃO ===")
    print("1 - Registrar partida")
    print("2 - Mostrar tabela")
    print("3 - Histórico")
    print("4 - Simular rodada aleatória")
    print("5 - Estatísticas de um time")
    print("6 - Sair")

    opcao = input("Escolha: ")

    if opcao == "1":

        print("Times disponíveis:")
        for t in times:
            print("-", t)

        casa = input("Time da casa: ")

        if casa not in tabela:
            print("Time inválido!")
            continue

        visitante = input("Time visitante: ")

        if visitante not in tabela:
            print("Time inválido!")
            continue

        if casa == visitante:
            print("Os times devem ser diferentes.")
            continue

        gols_casa = int(input(f"Gols do {casa}: "))
        gols_visitante = int(input(f"Gols do {visitante}: "))

        registrar_partida(casa, gols_casa, visitante, gols_visitante)

        print("Resultado registrado com sucesso!")

    elif opcao == "2":

        classificacao = sorted(
            tabela.items(),
            key=lambda x: (
                x[1]["P"],
                x[1]["SG"],
                x[1]["GP"]
            ),
            reverse=True
        )

        print("CLASSIFICAÇÃO")
        print("-" * 110)
        print(f"{'Pos':<4}{'Time':<20}{'Pts':<5}{'J':<4}{'V':<4}{'E':<4}{'D':<4}{'GP':<5}{'GC':<5}{'SG':<5}{'Zona':<20}")
        print("-" * 110)

        for pos, (time, dados) in enumerate(classificacao, start=1):
            print(
                f"{pos:<4}"
                f"{time:<20}"
                f"{dados['P']:<5}"
                f"{dados['J']:<4}"
                f"{dados['V']:<4}"
                f"{dados['E']:<4}"
                f"{dados['D']:<4}"
                f"{dados['GP']:<5}"
                f"{dados['GC']:<5}"
                f"{dados['SG']:<5}"
                f"{zona_classificacao(pos, len(times)):<20}"
            )

    elif opcao == "3":
        print("===== HISTÓRICO DE PARTIDAS =====")

        if len(historico) == 0:
            print("Nenhuma partida registrada.")
        else:
            for i, jogo in enumerate(historico, start=1):
                print(
                    f"{i}. "
                    f"{jogo['casa']} "
                    f"{jogo['gols_casa']} x "
                    f"{jogo['gols_visitante']} "
                    f"{jogo['visitante']}"
                )

    elif opcao == "4":
        simular_rodada()

    elif opcao == "5":
        print("Times disponíveis:")
        for t in times:
            print("-", t)

        time_escolhido = input("Qual time: ")

        if time_escolhido not in tabela:
            print("Time inválido!")
            continue

        mostrar_estatisticas_time(time_escolhido)

    elif opcao == "6":
        print("Fim do programa")
        break

    else:
        print("Opção inválida!")

