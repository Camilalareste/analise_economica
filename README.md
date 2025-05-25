import pandas as pd
import streamlit as st

# Upload do arquivo CSV
st.title("Análise Econômica (Gastos, Taxas & Insights)")

uploaded_file = st.file_uploader("Carregue seu arquivo CSV", type=["csv"])
if uploaded_file:
    df = pd.read_csv(uploaded_file)
    st.subheader("Visualização dos Dados")
    st.write(df)

    # Análise básica
    st.subheader("Insights Gerais")

    # Total de gastos
    if 'gastos' in df.columns:
        total_gastos = df['gastos'].sum()
        st.write(f"**Total de Gastos:** R$ {total_gastos:,.2f}")
    else:
        st.warning("Coluna 'gastos' não encontrada!")

    # Média das taxas
    if 'taxa' in df.columns:
        media_taxa = df['taxa'].mean()
        st.write(f"**Média das Taxas:** {media_taxa:.2f}%")
    else:
        st.warning("Coluna 'taxa' não encontrada!")

    # Gastos por categoria (se houver coluna 'categoria')
    if 'categoria' in df.columns and 'gastos' in df.columns:
        gastos_categoria = df.groupby('categoria')['gastos'].sum()
        st.subheader("Gastos por Categoria")
        st.bar_chart(gastos_categoria)
        st.write(gastos_categoria)
    else:
        st.info("Coluna 'categoria' ou 'gastos' não encontrada para análise por categoria.")

    # Exportar Insights para CSV
    st.subheader("Exportar Insights")
    insights = {
        "total_gastos": [total_gastos if 'gastos' in df.columns else None],
        "media_taxa": [media_taxa if 'taxa' in df.columns else None],
    }
    insights_df = pd.DataFrame(insights)
    st.write(insights_df)
    st.download_button(
        "Baixar Insights em CSV",
        insights_df.to_csv(index=False).encode('utf-8'),
        "insights_economicos.csv",
        "text/csv"
    )
else:
    st.info("Por favor, faça upload de um arquivo CSV para começar.")

# Exemplo de estrutura de CSV esperada:
# data,categoria,gastos,taxa
# 2024-01-01,Alimentação,150.50,1.5
# 2024-01-02,Transporte,80.00,2.0
