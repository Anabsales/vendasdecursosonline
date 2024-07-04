# vendasdecursosonline
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from flask import Flask, render_template_string

# Dados fornecidos pelo usuário
dados = {
    "ID": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25],
    "Nome do Curso": [
        "Introdução à Programação em Python", "Desenvolvimento Web com HTML e CSS",
        "JavaScript Avançado: Frameworks e Bibliotecas", "Introdução ao Machine Learning",
        "Desenvolvimento Mobile com React Native", "Arquitetura de Microserviços",
        "Banco de Dados SQL e NoSQL", "Segurança da Informação: Fundamentos",
        "Cloud Computing com AWS", "DevOps: Integração e Entrega Contínua",
        "Desenvolvimento Web com HTML e CSS", "JavaScript Avançado: Frameworks e Bibliotecas",
        "Introdução ao Machine Learning", "Desenvolvimento Mobile com React Native",
        "Arquitetura de Microserviços", "Banco de Dados SQL e NoSQL",
        "Segurança da Informação: Fundamentos", "Cloud Computing com AWS",
        "DevOps: Integração e Entrega Contínua", "Introdução à Programação em Python",
        "Desenvolvimento Web com HTML e CSS", "JavaScript Avançado: Frameworks e Bibliotecas",
        "Introdução ao Machine Learning", "Desenvolvimento Mobile com React Native",
        "Arquitetura de Microserviços"
    ],
    "Quantidade de Vendas": [
        50, 30, 20, 15, 25, 12, 18, 10, 22, 8, 20, 15, 10, 18, 8, 12, 5, 15, 6, 45,
        25, 18, 12, 20, 10
    ],
    "Preço Unitário": [
        39.90, 59.90, 79.90, 99.90, 69.90, 89.90, 79.90, 109.90, 99.90, 119.90, 59.90,
        79.90, 99.90, 69.90, 89.90, 79.90, 109.90, 99.90, 119.90, 39.90, 59.90, 79.90,
        99.90, 69.90, 89.90
    ],
    "Data": [
        "2023-01-01", "2023-01-02", "2023-01-03", "2023-01-04", "2023-01-05", "2023-01-06",
        "2023-01-07", "2023-01-08", "2023-01-09", "2023-01-10", "2023-01-11", "2023-01-12",
        "2023-01-13", "2023-01-14", "2023-01-15", "2023-01-16", "2023-01-17", "2023-01-18",
        "2023-01-19", "2023-01-20", "2023-01-21", "2023-01-22", "2023-01-23", "2023-01-24",
        "2023-01-25"
    ]
}

# Verificar se todas as listas têm o mesmo comprimento
comprimentos = {len(lista) for lista in dados.values()}
if len(comprimentos) != 1:
    raise ValueError("Todas as listas devem ter o mesmo comprimento")

# Criar DataFrame
df = pd.DataFrame(dados)

# Salvar em um arquivo Excel
file_path = 'c:/Users/Ana Luiza/OneDrive - carbigdata.com.br/Documentos/vendas_cursos_online.xlsx'
df.to_excel(file_path, index=False)

print(f"Planilha salva com sucesso em: {file_path}")

# Primeiras linhas do DataFrame
print(df.head())

# Informações básicas do conjunto
df_info = df.info()
print(df_info)
num_linhas, num_colunas = df.shape
print(f"Linhas: {num_linhas}, Colunas: {num_colunas}")

# Estatísticas Descritivas e Visualização dos Dados
titulo_barra = 'Quantidade de Vendas por Curso'
titulo_dispersao = 'Relação entre Quantidade de Vendas e Preço'
xgraphic = 'Quantidade de Vendas'
ygraphic_barra = 'Nome do Curso'
ygraphic_dispersao = 'Preço Unitário'

plt.figure(figsize=(13,9))
sns.barplot(x= 'Nome do Curso', y= 'Quantidade de Vendas' , data=df , ci=None)
plt.title(titulo_barra)
plt.xlabel(xgraphic)
plt.ylabel(ygraphic_barra)
plt.xticks(rotation=90)
plt.tight_layout()
plt.savefig('grafico_vendas.png')
plt.show()

plt.figure(figsize=(13,9))
sns.scatterplot(x= 'Preço Unitário', y= "Quantidade de Vendas", data = df)
plt.title(titulo_dispersao)
plt.xlabel(xgraphic)
plt.ylabel(ygraphic_dispersao)
plt.tight_layout()
plt.savefig('grafico_dispersao.png')
plt.show()

# Calculo Receita Total 
df['Receita Total'] = df['Quantidade de Vendas'] * df['Preço Unitário']
receita_total = df['Receita Total'].sum()
print(f"Receita Total: R$ {receita_total:.2f}")

# Identificar o Curso mais Vendido
curso_mais_vendido = df.loc[df['Quantidade de Vendas'].idxmax(), 'Nome do Curso']
print(f'Curso com Maior Número de Vendas: {curso_mais_vendido}')

# Visualizar a distribuição das vendas ao longo dos dias
df['Data'] = pd.to_datetime(df['Data'])
titulo_tempo = 'Distribuição de Vendas ao Longo do Tempo'
ylabel_tempo = 'Quantidade de Vendas'

plt.figure(figsize=(13,9))
sns.lineplot(x='Data', y= 'Quantidade de Vendas', data=df)
plt.title(titulo_tempo)
plt.xlabel('Data')
plt.ylabel(ylabel_tempo)
plt.tight_layout()
plt.savefig('grafico_tempo.png')
plt.show()

# Cálculos estatísticos
stats = {
    "Média": df["Quantidade de Vendas"].mean(),
    "Mediana": df["Quantidade de Vendas"].median(),
    "Mínimo": df["Quantidade de Vendas"].min(),
    "Máximo": df["Quantidade de Vendas"].max(),
    "Desvio Padrão": df["Quantidade de Vendas"].std()
}

# Criar aplicação Flask
app = Flask(__name__)

@app.route('/')
def index():
    stats_html = "".join([f"<p>{key}: {value:.2f}</p>" for key, value in stats.items()])
    return render_template_string("""
        <h1>Estatísticas de Vendas de Cursos</h1>
        <div>{{ stats_html|safe }}</div>
        <h2>Gráficos</h2>
        <img src="/static/grafico_vendas.png" alt="Gráfico de Vendas" style="width: 100%; height: auto;">
        <img src="/static/grafico_dispersao.png" alt="Gráfico de Dispersão" style="width: 100%; height: auto;">
        <img src="/static/grafico_tempo.png" alt="Gráfico de Vendas ao Longo do Tempo" style="width: 100%; height: auto;">
    """, stats_html=stats_html)

if __name__ == "__main__":
    app.run(debug=True)

# Salvar dados em um arquivo Excel
file_path = 'c:/Users/Ana Luiza/OneDrive - carbigdata.com.br/Documentos/vendas_cursos_online.xlsx'
df.to_excel(file_path, index=False)

print(f"Planilha salva com sucesso em: {file_path}")
