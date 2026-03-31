import streamlit as st
import numpy as np
import plotly.express as px
from collections import Counter

@st.cache_data
def predict_binary(history):
    """Advanced binary prediction algorithm"""
    if not history:
        return {"odd": 50.0, "even": 50.0, "recommend": "ANY", 
                "confidence": 0, "streak": 0, "total": 0}
    
    n = len(history)
    odd_count = sum(1 for x in history if x == 1)
    even_count = n - odd_count
    
    # Base probability
    base_odd = (odd_count / n) * 100
    
    # Recent bias (last 20%)
    recent_n = max(1, int(n * 0.2))
    recent = history[-recent_n:]
    recent_odd_pct = (sum(recent) / len(recent)) * 100
    
    # Streak detection
    streak = 1
    last = history[-1]
    for i in range(2, min(11, n+1)):
        if history[-i] == last:
            streak += 1
        else:
            break
    
    # Final prediction (weighted)
    prediction = (base_odd * 0.5 + recent_odd_pct * 0.4 + 
                 (50 + (50 - base_odd) * 0.1 if streak >= 4 else base_odd) * 0.1)
    
    confidence = min(abs(prediction - 50) * 2, 100)
    recommend = "🟢 ODD" if prediction > 51 else "🔴 EVEN" if prediction < 49 else "⚪ ANY"
    
    return {
        "odd": round(prediction, 1),
        "even": round(100-prediction, 1),
        "recommend": recommend,
        "confidence": round(confidence, 1),
        "streak": streak,
        "total": n
    }

def main():
    st.set_page_config(
        page_title="Binary Predictor",
        page_icon="🔮",
        layout="wide"
    )
    
    # Header
    st.title("🔮 BINARY ODD/EVEN PREDICTOR")
    st.markdown("**Pinakamataas na accuracy para sa binary games!** 🎯")
    
    # Sidebar
    with st.sidebar:
        st.header("📊 Quick Stats")
        st.info("✅ **Real-time analysis**")
        st.info("✅ **Streak detection**")
        st.info("✅ **Recent bias**")
        st.info("✅ **Mobile friendly**")
        
        if st.button("🎲 Random Test", use_container_width=True):
            st.cache_data.clear()
            np.random.seed(np.random.randint(1000))
            test_history = np.random.choice([0,1], size=25).tolist()
            st.session_state.test_history = test_history
    
    # Main content
    col1, col2, col3 = st.columns([1, 2, 1])
    
    with col1:
        st.subheader("🎮 INPUT")
        
        input_type = st.radio("Choose input method:", 
                             ["🔢 Manual Count", "📝 Paste History", "🎲 Auto Test"])
        
        if input_type == "🔢 Manual Count":
            odd = st.number_input("Odd (1) count:", 0, 1000, 5)
            even = st.number_input("Even (0) count:", 0, 1000, 5)
            history = [1]*odd + [0]*even
            
        elif input_type == "📝 Paste History":
            text = st.text_area("Paste your history (0 1 1 0 1):", 
                               placeholder="0 1 1 0 1 0 1", height=80)
            try:
                history = [int(x.strip()) for x in text.split() if x.strip() in ['0','1']]
            except:
                history = []
                st.error("⚠️ Invalid input! Use only 0 and 1 separated by space.")
        
        else:  # Auto Test
            if 'test_history' in st.session_state:
                history = st.session_state.test_history
            else:
                history = []
        
        if st.button("🚀 ANALYZE NOW", type="primary", use_container_width=True):
            if history:
                st.session_state.prediction = predict_binary(history)
                st.session_state.history = history
            else:
                st.warning("👆 Enter some data first!")
    
    with col2:
        st.subheader("🎯 YOUR PREDICTION")
        
        if 'prediction' in st.session_state:
            pred = st.session_state.prediction
            
            # Big recommendation
            st.markdown(f"""
            <div style='text-align:center; padding:20px; 
                       background:linear-gradient(45deg, #4CAF50, #45a049);
                       color:white; border-radius:15px; font-size:24px;'>
                **{pred['recommend']}**
                <div style='font-size:16px; margin-top:10px;'>
                    Confidence: **{pred['confidence']}%**
                </div>
            </div>
            """, unsafe_allow_html=True)
            
            # Percentages
            col_a, col_b = st.columns(2)
            with col_a:
                st.metric("🟢 ODD", f"{pred['odd']}%")
            with col_b:
                st.metric("🔴 EVEN", f"{pred['even']}%")
            
            # Stats
            st.info(f"📊 **{pred['total']} games** analyzed | "
                   f"🔥 Current streak: **{pred['streak']}**")
    
    with col3:
        if 'history' in st.session_state and st.session_state.history:
            history = st.session_state.history[-50:]  # Last 50
            fig = px.line(
                x=range(len(history)), 
                y=history, 
                title="📈 Recent History",
                labels={'y': 'Result (1=Odd, 0=Even)', 'x': 'Game #'},
                height=250,
                markers=True
            )
            fig.update_traces(line=dict(color='#1f77b4', width=3))
            fig.update_layout(showlegend=False, margin=dict(l=0,r=0,t=30,b=0))
            st.plotly_chart(fig, use_container_width=True)

if __name__ == "__main__":
    main()
