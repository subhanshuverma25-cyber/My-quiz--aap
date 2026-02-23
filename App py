import streamlit as st
import google.generativeai as genai
import pypdf

# --- APP CONFIGURATION ---
st.set_page_config(page_title="AI Exam Master", layout="wide")
st.title("🏗️ SSC/RRB JE AI Quiz App")

# API Key input (Security ke liye)
api_key = st.sidebar.text_input("Enter your Gemini API Key:", type="password")

if api_key:
    genai.configure(api_key=api_key)
    model = genai.GenerativeModel('gemini-1.5-flash')

    # --- PDF PROCESSING ---
    uploaded_file = st.file_uploader("Upload Study PDF for SSC/RRB JE", type="pdf")

    def get_pdf_text(file):
        reader = pypdf.PdfReader(file)
        text = ""
        for page in reader.pages:
            text += page.extract_text()
        return text

    # --- SESSION STATE (Data Store karne ke liye) ---
    if "quiz_data" not in st.session_state:
        st.session_state.quiz_data = None
    if "answered" not in st.session_state:
        st.session_state.answered = False

    # --- GENERATE QUESTION ---
    if uploaded_file and st.button("Generate Question from PDF"):
        pdf_content = get_pdf_text(uploaded_file)
        
        prompt = f"""
        Act as an SSC/RRB JE Expert. Based on this text: {pdf_content[:5000]}
        Generate 1 MCQ question. 
        Format strictly as follows:
        Question: [The question]
        A) [Option]
        B) [Option]
        C) [Option]
        D) [Option]
        Correct: [Letter only]
        Explanation: [Detailed explanation with latest IS codes if applicable]
        """
        
        response = model.generate_content(prompt)
        # Simple parsing logic (Aap ise aur improve kar sakte hain)
        st.session_state.quiz_data = response.text
        st.session_state.answered = False

    # --- DISPLAY INTERFACE ---
    if st.session_state.quiz_data:
        st.markdown("### 📝 Question")
        st.write(st.session_state.quiz_data.split("Correct:")[0])
        
        # User Answer Buttons
        col1, col2, col3, col4 = st.columns(4)
        with col1: a = st.button("A")
        with col2: b = st.button("B")
        with col3: c = st.button("C")
        with col4: d = st.button("D")

        if a or b or c or d:
            st.session_state.answered = True
            # Answer checking logic can be added here based on parsing
            st.info("💡 **Solution & Review Schedule:**")
            st.write(st.session_state.quiz_data.split("Correct:")[1])
            
            st.markdown("---")
            st.write("⏱️ **Spaced Repetition Timer:**")
            st.button("🔴 Hard (1 Min)")
            st.button("🟡 Moderate (1 Hour)")
            st.button("🟢 Mastered (1 Month)")
            
            if st.button("Next Question ➡️"):
                st.session_state.quiz_data = None
                st.rerun()

else:
    st.warning("Please enter your API Key in the sidebar to start.")

