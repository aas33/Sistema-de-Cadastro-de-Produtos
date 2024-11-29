# Sistema-de-Cadastro-de-Produtos em Linguagem C

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <conio.h>

#define MAX_PRODUTOS 100
#define MAX_TAM_NOME 50
#define MAX_TAM_SENHA 20

// Estrutura para armazenar o produto
typedef struct {
    int id;
    char nome[MAX_TAM_NOME];
    float preco;
} Produto;

// Função para limpar a tela (funciona no Windows)
void limparTela() {
    system("cls");
}

// Função para ocultar a senha enquanto o usuário digita
void digitarSenha(char *senha) {
    char ch;
    int i = 0;
    while (1) {
        ch = getch();  // Pega o caractere digitado
        if (ch == 13) {  // Se for ENTER
            senha[i] = '\0';
            break;
        } else if (ch == 8 && i > 0) {  // Se for BACKSPACE
            i--;
            printf("\b \b");
        } else {
            senha[i++] = ch;
            printf("*");
        }
    }
}

// Função para realizar o login
int fazerLogin() {
    char usuario[] = "admin";
    char senha[] = "1234"; // A senha está em texto plano, mas pode ser melhorada
    char usuarioInput[20], senhaInput[20];
    int tentativas = 0;

    while (tentativas < 3) {
        limparTela();
        printf("Digite o nome de usuario: ");
        scanf("%s", usuarioInput);
        printf("Digite a senha: ");
        digitarSenha(senhaInput);

        if (strcmp(usuarioInput, usuario) == 0 && strcmp(senhaInput, senha) == 0) {
            return 1;  // Login bem-sucedido
        } else {
            printf("\nUsuario ou senha incorretos! Tente novamente.\n");
            tentativas++;
            getch();
        }
    }
    return 0;  // Máximo de tentativas atingido
}

// Função para cadastrar um novo produto
void adicionarProduto(Produto *produtos, int *contagem) {
    limparTela();
    if (*contagem >= MAX_PRODUTOS) {
        printf("Capacidade maxima de produtos atingida!\n");
        return;
    }
    Produto p;
    p.id = *contagem + 1;
    printf("Digite o nome do produto: ");
    getchar();  // Para limpar o buffer do \n
    fgets(p.nome, MAX_TAM_NOME, stdin);
    p.nome[strcspn(p.nome, "\n")] = 0;  // Remover o \n do final
    printf("Digite o preco do produto: ");
    scanf("%f", &p.preco);
    produtos[*contagem] = p;
    (*contagem)++;
    printf("\nProduto adicionado com sucesso!\n");
    getch();
}

// Função para listar produtos em formato de tabela e calcular o total
void listarProdutos(Produto *produtos, int quantidade) {
    limparTela();
    float total = 0.0;

    printf("ID | Nome do Produto        | Preco\n");
    printf("----------------------------------------\n");
    for (int i = 0; i < quantidade; i++) {
        printf("%2d | %-20s | %.2f\n", produtos[i].id, produtos[i].nome, produtos[i].preco);
        total += produtos[i].preco;  // Acumula o preço para calcular o total
    }
    printf("----------------------------------------\n");
    printf("Total: %.2f\n", total);
    printf("\nPressione qualquer tecla para continuar...");
    getch();
}

// Função para excluir um produto
void excluirProduto(Produto *produtos, int *quantidade) {
    int id;
    limparTela();
    printf("Digite o ID do produto que deseja excluir: ");
    scanf("%d", &id);
    if (id < 1 || id > *quantidade) {
        printf("Produto nao encontrado!\n");
    } else {
        for (int i = id - 1; i < *quantidade - 1; i++) {
            produtos[i] = produtos[i + 1];  // Move os produtos para "apagar" o produto
        }
        (*quantidade)--;
        printf("Produto excluido com sucesso!\n");
    }
    getch();
}

// Função para alterar os dados de um produto
void alterarProduto(Produto *produtos, int quantidade) {
    int id;
    limparTela();
    printf("Digite o ID do produto que deseja alterar: ");
    scanf("%d", &id);
    if (id < 1 || id > quantidade) {
        printf("Produto nao encontrado!\n");
    } else {
        Produto *p = &produtos[id - 1];
        printf("Alterando produto: %s\n", p->nome);
        printf("Digite o novo nome do produto: ");
        getchar();  // Para limpar o buffer
        fgets(p->nome, MAX_TAM_NOME, stdin);
        p->nome[strcspn(p->nome, "\n")] = 0;  // Remover o \n do final
        printf("Digite o novo preco do produto: ");
        scanf("%f", &p->preco);
        printf("Produto alterado com sucesso!\n");
    }
    getch();
}

// Função de menu principal
void exibirMenu() {
    printf("1. Adicionar Produto\n");
    printf("2. Listar Produtos\n");
    printf("3. Excluir Produto\n");
    printf("4. Alterar Produto\n");
    printf("5. Sair\n");
}

int main() {
    Produto produtos[MAX_PRODUTOS];
    int quantidade = 0;

    if (!fazerLogin()) {
        printf("Numero de tentativas de login excedido! Programa encerrado.\n");
        return 1;
    }

    int opcao;
    do {
        limparTela();
        exibirMenu();
        printf("Escolha uma opcao: ");
        scanf("%d", &opcao);
        switch (opcao) {
            case 1:
                adicionarProduto(produtos, &quantidade);
                break;
            case 2:
                listarProdutos(produtos, quantidade);
                break;
            case 3:
                excluirProduto(produtos, &quantidade);
                break;
            case 4:
                alterarProduto(produtos, quantidade);
                break;
            case 5:
                printf("Saindo...\n");
                break;
            default:
                printf("Opcao invalida! Tente novamente.\n");
                getch();
        }
    } while (opcao != 5);

    return 0;
}

