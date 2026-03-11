import streamlit as st
import pandas as pd
from datetime import datetime

# --- CONFIGURAÇÃO VISUAL ---
st.set_page_config(page_title="Bella Lolla Academy", layout="wide", page_icon="🦋")

# Paleta de Cores Stitch Palettes (Extraída da sua imagem)
c_fundo = "#3b1712"       # Black Brown (Fundo)
c_dourado = "#e2a76f"     # Tan / Golden (Letras e Detalhes)
c_botao = "#924a40"       # Very Dark Terra Cotta
c_input = "#5c241c"       # Dark Rosewood
c_borda = "#bd7a44"       # Golden Brown

st.markdown(f"""
    <style>
    /* Estilo Geral */
    .stApp {{ background-color: {c_fundo}; }}
    
    /* Tipografia Dourada */
    h1, h2, h3, p, label, .stMarkdown {{ 
        color: {c_dourado} !important; 
        font-family: 'Playfair Display', serif; 
    }}
    
    /* Botões Luxuosos */
    .stButton>button {{ 
        background-color: {c_botao}; 
        color: white !important; 
        border-radius: 30px; 
        border: 1px solid {c_dourado}; 
        width: 100%; 
        font-weight: bold;
        transition: 0.3s;
    }}
    .stButton>button:hover {{ border: 2px solid white; transform: scale(1.02); }}

    /* Inputs Estilizados */
    .stTextInput div div input, .stNumberInput div div input {{
        background-color: {c_input}; 
        color: white !important; 
        border: 1px solid {c_borda};
        border-radius: 10px;
    }}

    /* Logotipo com Borboleta */
    .logo-container {{
        text-align: center; 
        padding: 40px; 
        border-bottom: 1px solid {c_borda}; 
        margin-bottom: 30px;
    }}
    .borboleta {{ font-size: 50px; margin-bottom: 10px; }}
    .logo-texto {{ 
        color: {c_dourado}; 
        font-size: 45px; 
        font-weight: bold; 
        letter-spacing: 4px;
        text-shadow: 2px 2px 4px rgba(0,0,0,0.5);
    }}
    .sub-logo {{ 
        color: {c_borda}; 
        font-size: 14px; 
        text-transform: uppercase; 
        letter-spacing: 2px;
    }}
    </style>
    """, unsafe_allow_html=True)

# --- LOGOTIPO EXCLUSIVO ---
st.markdown(f"""
    <div class="logo-container">
        <div class="borboleta">🦋</div>
        <div class="logo-texto">BELLA LOLLA</div>
        <div class="sub-logo">Studio & Academy • Transformando Olhares</div>
    </div>
    """, unsafe_allow_html=True)

# --- CONTROLE DE ACESSO ---
if "autenticado" not in st.session_state:
    st.session_state.autenticado = False

if not st.session_state.autenticado:
    _, col_login, _ = st.columns([1, 2, 1])
    with col_login:
        st.markdown("<h3 style='text-align: center;'>🔐 Área da Aluna</h3>", unsafe_allow_html=True)
        senha = st.text_input("Insira sua chave de acesso dourada:", type="password")
        if st.button("ENTRAR NO ACADEMY"):
            if senha == "BELLA10": # Senha para suas clientes
                st.session_state.autenticado = True
                st.rerun()
            else:
                st.error("Chave incorreta. Solicite ao suporte Bella Lolla.")
    st.stop()

# --- SISTEMA DE GESTÃO ---
if 'caixa' not in st.session_state:
    st.session_state.caixa = []

with st.sidebar:
    st.markdown("### 🎯 Meta Mensal")
    meta = st.number_input("Quanto deseja ganhar? (R$)", value=5000.0)
    st.divider()
    st.write("✨ *O sucesso é uma decisão.*")

aba1, aba2 = st.tabs(["💎 Calculadora de Envelopes", "📈 Histórico de Lucro"])

with aba1:
    c1, c2 = st.columns([1, 1.5])
    with c1:
        st.write("### ✍️ Novo Registro")
        proced = st.selectbox("Serviço", ["Cílios", "Sobrancelha", "Unhas", "Curso VIP"])
        valor = st.number_input("Valor do Serviço (R$)", value=200.0)
        custo_m = st.number_input("Custo de Material (R$)", value=40.0)
        meu_salario = st.number_input("Meu Salário (Mão de Obra) (R$)", value=100.0)
        lucro = valor - custo_m - meu_salario

        if st.button("REGISTRAR ATENDIMENTO 🦋"):
            st.session_state.caixa.append({
                "Data": datetime.now().strftime("%d/%m/%Y"),
                "Serviço": proced, "Valor": valor, "Salário": meu_salario, "Lucro": lucro
            })
            st.toast("Atendimento salvo com sucesso!", icon="✨")

    with c2:
        st.write("### 📂 Divisão dos Envelopes")
        st.info(f"💰 **Envelope MEU SALÁRIO:** R$ {meu_salario:.2f}")
        st.success(f"📦 **Envelope REPOSIÇÃO:** R$ {custo_m:.2f}")
        st.warning(f"🏦 **Envelope LUCRO EMPRESA:** R$ {lucro:.2f}")

with aba2:
    if st.session_state.caixa:
        df = pd.DataFrame(st.session_state.caixa)
        total_s = df['Salário'].sum()
        prog = (total_s / meta)
        
        st.write("### 📊 Evolução Bella Lolla")
        st.metric("Salário Acumulado", f"R$ {total_s:.2f}", delta=f"{(prog*100):.1f}% da meta")
        st.progress(min(prog, 1.0))
        
        st.table(df)
        
        csv = df.to_csv(index=False).encode('utf-8')
        st.download_button("📥 Baixar Relatório Mensal", csv, "caixa_bella_lolla.csv")
    else:
        st.info("Nenhum registro encontrado. Comece agora! 🦋")
