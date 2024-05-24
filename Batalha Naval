import java.io.File
import java.io.PrintWriter

const val MENU_PRINCIPAL = 100
const val MENU_DEFINIR_TABULEIRO = 101
const val MENU_DEFINIR_NAVIOS = 102
const val MENU_JOGAR = 103
const val MENU_LER_FICHEIRO = 104
const val MENU_GRAVAR_FICHEIRO = 105
const val SAIR = 106

var numLinhas = -1
var numColunas = -1

var tabuleiroHumano: Array<Array<Char?>> = emptyArray()
var tabuleiroComputador: Array<Array<Char?>> = emptyArray()

var tabuleiroPalpitesDoHumano: Array<Array<Char?>> = emptyArray()
var tabuleiroPalpitesDoComputador: Array<Array<Char?>> = emptyArray()

fun calculaNumNavios(numLinhas: Int, numColunas: Int): Array<Int>{
    if (numLinhas == numColunas){
        return when(numLinhas){
            4 -> arrayOf(2,0,0,0)
            5 -> arrayOf(1,1,1,0)
            7 -> arrayOf(2,1,1,1)
            8 -> arrayOf(2,2,1,1)
            10 -> arrayOf(3,2,1,1)
            else -> arrayOf()
        }
    }
    return arrayOf()
}

fun criaTabuleiroVazio(numLinhas: Int, numColunas: Int): Array<Array<Char?>> {
    return Array(numLinhas) { Array<Char?>(numColunas) { null } }
}

fun coordenadaContida(tabuleiros: Array<Array<Char?>>, linha: Int, coluna: Int): Boolean  {
    return linha in 1..tabuleiros.size && coluna in 1..tabuleiros[0].size
}

fun limparCoordenadasVazias(array: Array<Pair<Int,Int>>): Array<Pair<Int,Int>>{
    var resultado: Array<Pair<Int,Int>> = arrayOf()
    for(posicao in array.indices){
        if (array[posicao] != Pair(0,0)){
            resultado += array[posicao]
        }
    }
    return resultado
}

fun juntarCoordenadas(array1: Array<Pair<Int,Int>>, array2: Array<Pair<Int,Int>>): Array<Pair<Int,Int>>{
    return array1 + array2
}

fun gerarCoordenadasNavio(tabuleiros: Array<Array<Char?>>, linha: Int, coluna: Int, orientacao: String, dimensao: Int): Array<Pair<Int,Int>> {
    val tamanhoLinha = tabuleiros.size
    val tamanhoColuna = tabuleiros[0].size
    val coordenadas = Array<Pair<Int,Int>>(dimensao) { Pair(0, 0) }
    if (linha in 1..tamanhoLinha && coluna in 1..tamanhoColuna) {
        when (orientacao) {
            "N" -> {
                for (posicao in 0 until dimensao) {
                    coordenadas[posicao] = Pair(linha - posicao, coluna)
                    if (linha - posicao !in 1..tamanhoLinha) {
                        return arrayOf()
                    }
                }
            }
            "S" -> {
                for (posicao in 0 until dimensao) {
                    coordenadas[posicao] = Pair(linha + posicao, coluna)
                    if (linha + posicao !in 1..tamanhoLinha) {
                        return arrayOf()
                    }
                }
            }

            "E" -> {
                for (posicao in 0 until dimensao) {
                    coordenadas[posicao] = Pair(linha, coluna + posicao)
                    if (coluna + posicao !in 1..tamanhoColuna) {
                        return arrayOf()
                    }
                }
            }

            "O" -> {
                for (posicao in 0 until dimensao) {
                    coordenadas[posicao] = Pair(linha, coluna - posicao)
                    if (coluna - posicao !in 1..tamanhoColuna) {
                        return arrayOf()
                    }
                }
            }
        }
        return coordenadas
    }
    return arrayOf()
}

fun gerarCoordenadasFronteira(tabuleiro: Array<Array<Char?>>, linha: Int, coluna: Int, orientacao: String, dimensao: Int): Array<Pair<Int,Int>> {
    val coordenadasFronteira = Array<Pair<Int, Int>>(dimensao * 10) { Pair(0, 0) }
    var posicao = 0
    val coordenadasNavio = gerarCoordenadasNavio(tabuleiro, linha, coluna, orientacao, dimensao)
    when (orientacao) {
        "N" -> {
            for (linhas in linha - dimensao..linha + 1) {
                for (colunas in coluna - 1..coluna + 1) {
                    if (linhas in 1..tabuleiro.size && colunas in 1..tabuleiro[0].size) {
                        if(Pair(linhas,colunas) !in coordenadasNavio){
                            coordenadasFronteira[posicao] = Pair(linhas, colunas)
                        }
                        posicao++
                    }
                }
            }
        }
        "S" -> {
            for (linhas in linha - 1..linha + dimensao) {
                for (colunas in coluna - 1..coluna + 1) {
                    if (linhas in 1..tabuleiro.size && colunas in 1..tabuleiro[0].size) {
                        if(Pair(linhas,colunas) !in coordenadasNavio){
                            coordenadasFronteira[posicao] = Pair(linhas, colunas)
                        }
                        posicao++
                    }
                }
            }
        }
        "E" -> {
            for (linhas in linha - 1..linha + 1) {
                for (colunas in coluna - 1..coluna + dimensao) {
                    if (linhas in 1..tabuleiro.size && colunas in 1..tabuleiro[0].size) {
                        if(Pair(linhas,colunas) !in coordenadasNavio){
                            coordenadasFronteira[posicao] = Pair(linhas, colunas)
                        }
                        posicao++
                    }
                }
            }
        }
        "O" -> {
            for (linhas in linha - 1..linha + 1) {
                for (colunas in coluna - dimensao..coluna + 1) {
                    if(linhas in 1..tabuleiro.size && colunas in 1..tabuleiro[0].size){
                        if(Pair(linhas,colunas) !in coordenadasNavio){
                            coordenadasFronteira[posicao] = Pair(linhas, colunas)
                        }
                        posicao++
                    }
                }
            }
        }
    }
    return limparCoordenadasVazias(coordenadasFronteira)
}

fun estaLivre(tabuleiro: Array<Array<Char?>>, array: Array<Pair<Int,Int>>): Boolean {
    for(coordenada in array) {
        val linha = coordenada.first
        val coluna = coordenada.second
        if (!coordenadaContida(tabuleiro,linha,coluna)){
            return false
        }
        if(tabuleiro[linha - 1][coluna - 1] != null){
            return false
        }
    }
    return true
}

fun insereNavioSimples(tabuleiro: Array<Array<Char?>>, linha: Int, coluna: Int, dimensao: Int): Boolean {
    val coordenadasNavio = gerarCoordenadasNavio(tabuleiro,linha,coluna,"E",dimensao)
    val coordenadasFronteira = gerarCoordenadasFronteira(tabuleiro,linha,coluna,"E",dimensao)
    val juntarCoordenadas = juntarCoordenadas(coordenadasNavio,coordenadasFronteira)
    if (coordenadasNavio.isEmpty()){
        return false
    }
    val navio: Char = when(dimensao){
        1 -> '1'
        2 -> '2'
        3 -> '3'
        else -> '4'
    }
    if(coordenadaContida(tabuleiro,linha,coluna)){
        if(estaLivre(tabuleiro,juntarCoordenadas)){
            for(coordenadas in coordenadasNavio){
                val linhas = coordenadas.first - 1
                val colunas = coordenadas.second - 1
                tabuleiro[linhas][colunas] = navio
            }
            return true
        }
    }
    return false
}

fun insereNavio(tabuleiro: Array<Array<Char?>>, linha: Int, coluna: Int, orientacao: String, dimensao: Int): Boolean {
    val coordenadasNavio = gerarCoordenadasNavio(tabuleiro,linha,coluna,orientacao,dimensao)
    val coordenadasFronteira = gerarCoordenadasFronteira(tabuleiro,linha,coluna,orientacao,dimensao)
    val juntarCoordenadas = juntarCoordenadas(coordenadasNavio,coordenadasFronteira)
    if (coordenadasNavio.isEmpty()){
        return false
    }
    val navio: Char = when(dimensao){
        1 -> '1'
        2 -> '2'
        3 -> '3'
        else -> '4'
    }
    if(coordenadaContida(tabuleiro,linha,coluna)){
        if(estaLivre(tabuleiro,juntarCoordenadas)){
            for(coordenadas in coordenadasNavio){
                val linhas = coordenadas.first - 1
                val colunas = coordenadas.second - 1
                tabuleiro[linhas][colunas] = navio
            }
            return true
        }
    }
    return false
}

fun preencheTabuleiroComputador(tabuleiro: Array<Array<Char?>>, navios: Array<Int>) {
    for (tamanhoNavio in navios.indices) {
        var naviosRestantes = navios[tamanhoNavio]
        val orientacoes = arrayOf("N", "S", "O", "E")
        while (naviosRestantes > 0) {
            val linha = (0 until tabuleiro.size).random()
            val coluna = (0 until tabuleiro[0].size).random()
            val orientacao = orientacoes.random()
            if (insereNavio(tabuleiro, linha + 1, coluna + 1, orientacao, tamanhoNavio + 1)) {
                naviosRestantes--
            }
        }
    }
}

fun navioCompleto(tabuleiro: Array<Array<Char?>>, linha: Int, coluna: Int): Boolean {
    val navioAtual = tabuleiro[linha - 1][coluna - 1]

    fun verificaNavioNaDirecao(direcaoLinha: Int, direcaoColuna: Int): Boolean {
        var linhaAtual = linha
        var colunaAtual = coluna
        var tamanhoNavio = '0'

        while (coordenadaContida(tabuleiro, linhaAtual, colunaAtual) &&
            tabuleiro[linhaAtual - 1][colunaAtual - 1] == navioAtual) {
            tamanhoNavio++
            linhaAtual += direcaoLinha
            colunaAtual += direcaoColuna
        }

        linhaAtual = linha - direcaoLinha
        colunaAtual = coluna - direcaoColuna

        while (coordenadaContida(tabuleiro, linhaAtual, colunaAtual) &&
            tabuleiro[linhaAtual - 1][colunaAtual - 1] == navioAtual) {
            tamanhoNavio++
            linhaAtual -= direcaoLinha
            colunaAtual -= direcaoColuna
        }
        return tamanhoNavio == (navioAtual)
    }
    return (coordenadaContida(tabuleiro, linha, coluna) && (verificaNavioNaDirecao(0, 1) ||
            verificaNavioNaDirecao(0, -1) ||
            verificaNavioNaDirecao(1, 0) || verificaNavioNaDirecao(-1, 0)))
}

fun obtemMapa(tabuleiro: Array<Array<Char?>>, valor: Boolean): Array<String> {
    val linhas = tabuleiro.size
    val colunas = tabuleiro[0].size
    val mapa = Array(linhas + 1) { "" }
    mapa[0] = "| ${criaLegendaHorizontal(colunas)} |"

    for (nLinha in 1..linhas) {
        for (nColuna in 1..colunas) {
            val navio = tabuleiro[nLinha - 1][nColuna - 1]
            if (navio == null) {
                mapa[nLinha] += if(valor) "| ~ " else "| ? "
            } else if (!valor) {
                val numeroPequeno = when {
                    navio == '2' && !navioCompleto(tabuleiro, nLinha, nColuna) -> '₂'
                    navio == '3' && !navioCompleto(tabuleiro, nLinha, nColuna) -> '₃'
                    navio == '4' && !navioCompleto(tabuleiro, nLinha, nColuna) -> '₄'
                    else -> navio
                }
                mapa[nLinha] += "| $numeroPequeno "
            } else {
                mapa[nLinha] += "| $navio "
            }
        }
        mapa[nLinha] += "| $nLinha"
    }

    return mapa
}

fun lancarTiro(tabuleiro1: Array<Array<Char?>>, tabuleiro2: Array<Array<Char?>>, coordenadas: Pair<Int, Int>): String {
    val linha = coordenadas.first - 1
    val coluna = coordenadas.second - 1
    tabuleiro2[linha][coluna] = when (tabuleiro1[linha][coluna]){
        null -> 'X'
        '1' -> '1'
        '2' -> '2'
        '3' -> '3'
        else -> '4'
    }
    return when (tabuleiro2[linha][coluna]) {
        'X' -> "Agua."
        '1' -> "Tiro num submarino."
        '2' -> "Tiro num contra-torpedeiro."
        '3' -> "Tiro num navio-tanque."
        else -> "Tiro num porta-aviões."
    }
}

fun geraTiroComputador(tabuleiro: Array<Array<Char?>>): Pair<Int, Int> {
    val linhas = 0 until tabuleiro.size
    val colunas = 0 until tabuleiro[0].size
    var linha = linhas.random()
    var coluna = colunas.random()
    while (tabuleiro[linha][coluna] != null) {
        linha = linhas.random()
        coluna = colunas.random()
    }
    return Pair(linha + 1, coluna + 1)
}

fun contarNaviosDeDimensao(tabuleiro: Array<Array<Char?>>, dimensao: Int): Int {
    var contadorNavios = 0
    var tamanho = '0'
    var naviosContados: Array<Pair<Int, Int>> = arrayOf()

    tamanho = when (dimensao) {
        1 -> '1'
        2 -> '2'
        3 -> '3'
        else -> '4'
    }

    for (linha in 1..tabuleiro.size) {
        for (coluna in 1..tabuleiro[linha - 1].size) {
            if (tabuleiro[linha - 1][coluna - 1] == tamanho) {
                val posicao = Pair(linha, coluna)
                if (posicao !in naviosContados &&
                    navioCompleto(tabuleiro, linha, coluna)
                ) {
                    naviosContados += marcarNavioContado(linha, coluna, dimensao)
                    contadorNavios++
                }
            }
        }
    }
    return contadorNavios
}

fun marcarNavioContado(linha: Int, coluna: Int, dimensao: Int,) : Array<Pair<Int, Int>> {
    var naviosPassados: Array<Pair<Int, Int>> = arrayOf()
    for (i in 0 until dimensao) {
        naviosPassados += (Pair(linha + i, coluna))
    }
    for (i in 0 until dimensao) {
        naviosPassados += (Pair(linha, coluna + i))
    }
    for (i in 0 until dimensao) {
        naviosPassados += (Pair(linha - i, coluna))
    }
    for (i in 0 until dimensao) {
        naviosPassados += (Pair(linha, coluna - i))
    }
    return naviosPassados
}

fun venceu(tabuleiro: Array<Array<Char?>>): Boolean {
    val numLinhas = tabuleiro.size
    val numColunas = tabuleiro[0].size
    val numNavios = calculaNumNavios(numLinhas, numColunas)

    for (dimensao in 1..numNavios.size) {
        val naviosEsperados = numNavios[dimensao - 1]
        val naviosEncontrados = contarNaviosDeDimensao(tabuleiro, dimensao)

        if (naviosEsperados != naviosEncontrados) {
            return false
        }
    }
    return true
}

fun lerJogo(ficheiro: String, tipoTabuleiro: Int): Array<Array<Char?>> {
    val ficheiroGravado = File(ficheiro).readLines()

    numLinhas = ficheiroGravado[0][0].digitToInt()
    numColunas = ficheiroGravado[0][2].digitToInt()

    val linhasNoFicheiro = 3 * tipoTabuleiro + 1 + numLinhas * (tipoTabuleiro - 1) until 3 * tipoTabuleiro + 1 + numLinhas * tipoTabuleiro
    val tabuleiro = criaTabuleiroVazio(numLinhas,numColunas)
    var linha = linhasNoFicheiro.min()
    while(linha in linhasNoFicheiro){
        var coluna = 0
        var contaVirgulas = 0
        while (contaVirgulas < numColunas && coluna < ficheiroGravado[linha].length){
            when (ficheiroGravado[linha][coluna]){
                ',' -> contaVirgulas++
                '1','2','3','4','X' -> tabuleiro[linha - linhasNoFicheiro.min()][contaVirgulas] = ficheiroGravado[linha][coluna]
            }
            coluna++
        }
        linha++
    }
    when(tipoTabuleiro) {
        1 -> tabuleiroHumano = tabuleiro
        2 -> tabuleiroPalpitesDoHumano = tabuleiro
        3 -> tabuleiroComputador = tabuleiro
        4 -> tabuleiroPalpitesDoComputador = tabuleiro
    }
    return tabuleiro
}

fun imprimirTabuleiro(filePrinter: PrintWriter, tabuleiro: Array<Array<Char?>>) {
    for (linha in tabuleiro) {
        var count = 1
        for (coluna in linha) {
            if (count != tabuleiro.size) {
                filePrinter.print("${coluna ?: ""},")
                count++
            } else {
                filePrinter.print("${coluna ?: ""}")
            }
        }
        filePrinter.println()
    }
}

fun gravarJogo(ficheiro: String, tabuleiroRealHumano: Array<Array<Char?>>, tabuleiroPalpitesDoHumano: Array<Array<Char?>>,
               tabuleiroRealComputador: Array<Array<Char?>>, tabuleiroPalpitesDoComputador: Array<Array<Char?>>) {
    val filePrinter = File(ficheiro).printWriter()

    filePrinter.println("${tabuleiroRealHumano.size},${tabuleiroRealHumano[0].size}\n")

    filePrinter.println("Jogador\nReal")
    imprimirTabuleiro(filePrinter, tabuleiroRealHumano)

    filePrinter.println("\nJogador\nPalpites")
    imprimirTabuleiro(filePrinter, tabuleiroPalpitesDoHumano)

    filePrinter.println("\nComputador\nReal")
    imprimirTabuleiro(filePrinter, tabuleiroRealComputador)

    filePrinter.println("\nComputador\nPalpites")
    imprimirTabuleiro(filePrinter, tabuleiroPalpitesDoComputador)

    filePrinter.close()
}

fun tamanhoTabuleiroValido(numLinhas: Int, numColunas: Int): Boolean {
    if (numLinhas == numColunas) {
        return when (numLinhas) {
            4, 5, 7, 8, 10 -> true
            else -> false
        }
    }
    return false
}

fun processaCoordenadas(coordenadas: String, numLinhas: Int, numColunas: Int): Pair<Int, Int>? {
    if (coordenadas.length in 3..4) {
        if (coordenadas.length == 3) {
            val linhas = (coordenadas[0].toString()).toIntOrNull() ?: return null
            val colunas = coordenadas[2]
            if (linhas in 1..numLinhas && colunas in 'A'..'A' + numColunas - 1) {
                val coluna = colunas - 'A' + 1
                return Pair(linhas, coluna)
            }
        } else {
            val linhas = (coordenadas[0].toString() + coordenadas[1].toString()).toIntOrNull() ?: return null
            val colunas = coordenadas[3]
            if (linhas in 1..numLinhas && colunas in 'A'..'A' + numColunas - 1) {
                val coluna = colunas - 'A' + 1
                return Pair(linhas, coluna)
            }
        }
    }
    return null
}

fun criaLegendaHorizontal(numColunas: Int): String {
    var count = 0
    var resultado = ""
    while (count < numColunas){
        resultado += "${(65 + count).toChar()}"
        count++
        if (count < numColunas) {
            resultado += " | "
        }
    }
    return resultado
}

fun menuPrincipal(): Int {
    var numeros = 0
    println("\n> > Batalha Naval < <\n\n" +
            "1 - Definir Tabuleiro e Navios\n" +
            "2 - Jogar\n" +
            "3 - Gravar\n" +
            "4 - Ler\n" +
            "0 - Sair\n")
    do {
        val numero = readln().toIntOrNull()

        if (numero == null || numero !in 0..4) {
            println("!!! Opcao invalida, tente novamente")
        } else {
            numeros = when (numero) {
                1 -> MENU_DEFINIR_TABULEIRO
                2 -> MENU_JOGAR
                3 -> MENU_GRAVAR_FICHEIRO
                4 -> MENU_LER_FICHEIRO
                0 -> SAIR
                else -> MENU_PRINCIPAL
            }
        }
    } while (numero == null || numero !in 0..4)

    return numeros
}

fun menuDefinirTabuleiro(): Int{
    println("\n> > Batalha Naval < <\n" +
            "\nDefina o tamanho do tabuleiro:")
    var linhas: Int?
    var colunas: Int?
    do {
        do {
            println("Quantas linhas?")
            linhas = readln().toIntOrNull()
            when (linhas) {
                -1 -> return MENU_PRINCIPAL
                null, !in 1..26 -> println("!!! Número de linhas inválidas, tente novamente")
            }
        } while (linhas == null || linhas !in 1..26)
        do {
            println("Quantas colunas?")
            colunas = readln().toIntOrNull()
            when (colunas) {
                -1 -> return MENU_PRINCIPAL
                null, !in 1..26 -> println("!!! Número de colunas inválidas, tente novamente")
            }
        } while (colunas == null || colunas !in 1..26)
        numLinhas = linhas
        numColunas = colunas

        if (!tamanhoTabuleiroValido(numLinhas, numColunas)) {
            println("Tabuleiro invalido,tente novamente")
        }
    } while(!tamanhoTabuleiroValido(numLinhas, numColunas))

    tabuleiroHumano = criaTabuleiroVazio(numLinhas, numColunas)
    tabuleiroComputador = criaTabuleiroVazio(numLinhas, numColunas)
    tabuleiroPalpitesDoHumano = criaTabuleiroVazio(numLinhas, numColunas)
    tabuleiroPalpitesDoComputador = criaTabuleiroVazio(numLinhas, numColunas)
    val navios = calculaNumNavios(numLinhas, numColunas)
    preencheTabuleiroComputador(tabuleiroComputador, navios)

    for (mapa in obtemMapa(tabuleiroHumano, true)) println(mapa)

    return MENU_DEFINIR_NAVIOS
}

fun menuDefinirNavios(): Int{
    val navios = calculaNumNavios(numLinhas, numColunas)
    for (dimensao in 1 .. navios.size) {
        var naviosRestantes = navios[dimensao - 1]
        while (naviosRestantes > 0) {
            val tipoDeNavio = when (dimensao){
                1 -> "submarino:"
                2 -> "contra-torpedeiro:"
                3 -> "navio-tanque:"
                else -> "porta-avioes:"
            }
            println("Insira as coordenadas de um $tipoDeNavio\nCoordenadas? (ex: 6,G)")
            var coordenadas = readln()
            if (coordenadas == "-1") return MENU_PRINCIPAL

            while (processaCoordenadas(coordenadas, numLinhas, numColunas) == null) {
                println("!!! Coordenadas invalidas, tente novamente\nCoordenadas? (ex: 6,G)")
                coordenadas = readln()
            }
            if (dimensao != 1){
                println("Insira a orientacao do navio:\nOrientacao? (N, S, E, O)")
                var orientacao = readln()
                if (orientacao == "-1") return MENU_PRINCIPAL

                while (!(orientacao == "N" || orientacao == "S" || orientacao == "E" || orientacao == "O")) {
                    println("!!! Orientacao invalida, tente novamente\nOrientacao? (N, S, E, O)")
                    orientacao = readln()
                }
                val coordenadaHumano = processaCoordenadas(coordenadas, numLinhas,numColunas)
                if (insereNavio(tabuleiroHumano, coordenadaHumano!!.first , coordenadaHumano.second,orientacao,dimensao)){
                    naviosRestantes--
                    for (posicao in (obtemMapa(tabuleiroHumano, true))) {
                        println(posicao)
                    }
                } else {
                    println("!!! Posicionamento invalido, tente novamente.")
                }
            }else{
                val coordHumano = processaCoordenadas(coordenadas, numLinhas, numColunas)
                if (insereNavioSimples(tabuleiroHumano, coordHumano!!.first, coordHumano.second, dimensao)) {
                    naviosRestantes--
                    for (posicao in (obtemMapa(tabuleiroHumano, true))) {
                        println(posicao)
                    }
                } else {
                    println("!!! Posicionamento invalido, tente novamente.")
                }
            }
        }
    }
    println("Pretende ver o mapa gerado para o Computador? (S/N)")
    val mostrarMapa = readln()
    if (mostrarMapa == "-1") return MENU_PRINCIPAL

    if (mostrarMapa == "S") {
        for (mapa in obtemMapa(tabuleiroComputador, true)) println(mapa)
    }
    return MENU_PRINCIPAL
}

fun menuJogar(): Int {
    if(tabuleiroHumano.isEmpty() && tabuleiroComputador.isEmpty()) {
        println("!!! Tem que primeiro definir o tabuleiro do jogo, tente novamente")
    } else {
        var enter = ""
        var coordenadas = ""
        do {
            for (mapa in obtemMapa(tabuleiroPalpitesDoHumano, false)) println(mapa)
            do {
                println("Indique a posição que pretende atingir\nCoordenadas? (ex: 6,G)")
                coordenadas = readln()
            } while(coordenadas == null)

            if(coordenadas == "-1") return MENU_PRINCIPAL
            val coordHumano = processaCoordenadas(coordenadas, numLinhas, numColunas)
            if (coordHumano != null) {
                val lancarTiroHumano = lancarTiro(tabuleiroComputador, tabuleiroPalpitesDoHumano, coordHumano)
                if (navioCompleto(tabuleiroPalpitesDoHumano, coordHumano.first, coordHumano.second)) {
                    println(">>> HUMANO >>>$lancarTiroHumano Navio ao fundo!")
                } else {
                    println(">>> HUMANO >>>$lancarTiroHumano")
                }
            }
            if(!venceu(tabuleiroPalpitesDoHumano)) {
                val coordPC = geraTiroComputador(tabuleiroPalpitesDoComputador)
                val lancarTiroComputador = lancarTiro(tabuleiroHumano, tabuleiroPalpitesDoComputador, coordPC)

                println("Computador lancou tiro para a posicao $coordPC" +
                        "\n>>> Computador >>>$lancarTiroComputador" +
                        "\nPrima enter para continuar")
                enter = readln()
            }

        } while (!venceu(tabuleiroPalpitesDoComputador) && !venceu(tabuleiroPalpitesDoHumano))
        if(venceu(tabuleiroPalpitesDoHumano)){
            println("PARABENS! Venceu o jogo!")
        } else {
            println("OPS! O computador venceu o jogo!")
        }
        println("Prima enter para voltar ao menu principal")
        enter = readln()
    }
    return MENU_PRINCIPAL
}

fun menuLerFicheiro(): Int{
    println("Introduza o nome do ficheiro (ex: jogo.txt)")
    val ficheiro = readln()
    lerJogo(ficheiro,1)
    lerJogo(ficheiro,2)
    lerJogo(ficheiro,3)
    lerJogo(ficheiro,4)
    println("Tabuleiro ${lerJogo(ficheiro,1).size}x${lerJogo(ficheiro,1)[0].size} lido com sucesso")
    for (mapa in obtemMapa(tabuleiroHumano, true)) println(mapa)
    return MENU_PRINCIPAL
}

fun menuGravarJogo(): Int{
    if(tabuleiroHumano.isEmpty() && tabuleiroComputador.isEmpty()){
        println("!!! Tem que definir o tabuleiro do jogo, tente novamente")
        return MENU_PRINCIPAL
    }
    println("Introduza o nome do ficheiro (ex: jogo.txt)")
    val ficheiro = readln()
    gravarJogo(ficheiro,tabuleiroHumano,tabuleiroPalpitesDoHumano,tabuleiroComputador,tabuleiroPalpitesDoComputador)
    println("Tabuleiro ${tabuleiroHumano.size}x${tabuleiroHumano[0].size} gravado com sucesso")
    return MENU_PRINCIPAL
}

fun main() {
    var menuAtual = MENU_PRINCIPAL
    while (true) {
        menuAtual = when (menuAtual) {
            MENU_PRINCIPAL -> menuPrincipal()
            MENU_DEFINIR_TABULEIRO -> menuDefinirTabuleiro()
            MENU_DEFINIR_NAVIOS -> menuDefinirNavios()
            MENU_JOGAR -> menuJogar()
            MENU_LER_FICHEIRO -> menuLerFicheiro()
            MENU_GRAVAR_FICHEIRO -> menuGravarJogo()
            SAIR -> return
            else -> return
        }
    }
}
