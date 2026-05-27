import streamlit as st
from PIL import Image
import google.generativeai as genai
import io

# 페이지 설정
st.set_page_config(
    page_title="수학 문제 변형기 🧮",
    page_icon="📐",
    layout="centered"
)

# CSS 스타일링
st.markdown("""
    <style>
    .stApp {
        background: linear-gradient(135deg, #f5f7fa 0%, #e8f4f8 100%);
    }
    .title-text {
        text-align: center;
        font-size: 2.8em;
        background: linear-gradient(90deg, #667eea, #764ba2);
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        font-weight: bold;
        padding: 20px;
    }
    .problem-card {
        background: white;
        padding: 25px;
        border-radius: 20px;
        margin: 15px 0;
        box-shadow: 0 4px 15px rgba(102, 126, 234, 0.15);
        border-left: 5px solid #667eea;
    }
    .info-box {
        background: linear-gradient(135deg, #fff5f5, #fef0f0);
        padding: 15px;
        border-radius: 15px;
        border-left: 4px solid #ff6b9d;
    }
    </style>
""", unsafe_allow_html=True)

# 헤더
st.markdown('<div class="title-text">📐 수학 문제 변형기 🔢</div>', unsafe_allow_html=True)
st.markdown("### ✨ 사진 한 장으로 새로운 문제를 만들어보세요! 🎯")
st.markdown("---")

# 사이드바
with st.sidebar:
    st.markdown("## 🔑 API 키 설정")
    api_key = st.text_input(
        "Google Gemini API Key 🗝️",
        type="password",
        help="https://aistudio.google.com/app/apikey 에서 발급받으세요!"
    )
    
    st.markdown("---")
    st.markdown("## 🎈 사용 방법")
    st.info("""
    1️⃣ API 키를 입력하세요  
    2️⃣ 수학 문제 사진 업로드 📸  
    3️⃣ 난이도와 옵션 선택 🎚️  
    4️⃣ 변형 문제 생성! ✨  
    """)
    
    st.markdown("## 💡 학습 팁")
    st.success("""
    🌟 변형된 문제를 직접 풀어보세요!  
    🌟 풀이 과정을 손으로 적어보세요!  
    🌟 원본과 비교하며 개념을 익히세요!  
    """)

# API 키 확인
if not api_key:
    st.warning("⚠️ 사이드바에서 Google Gemini API 키를 먼저 입력해주세요! 🔑")
    st.markdown("""
    <div class='info-box'>
        <h4>🌐 API 키 발급 방법</h4>
        <ol>
            <li>👉 <a href='https://aistudio.google.com/app/apikey' target='_blank'>Google AI Studio</a> 접속</li>
            <li>🔐 Google 계정으로 로그인</li>
            <li>✨ "Create API Key" 클릭</li>
            <li>📋 발급된 키를 복사해서 사이드바에 붙여넣기</li>
        </ol>
    </div>
    """, unsafe_allow_html=True)
    st.stop()

# Gemini 설정
try:
    genai.configure(api_key=api_key)
    model = genai.GenerativeModel('gemini-2.0-flash-exp')
except Exception as e:
    st.error(f"❌ API 키 설정 오류: {e}")
    st.stop()

# 메인 영역
col1, col2 = st.columns(2)

with col1:
    st.markdown("### 📸 문제 사진 업로드")
    uploaded_file = st.file_uploader(
        "수학 문제 이미지를 올려주세요! 🖼️",
        type=['png', 'jpg', 'jpeg'],
        help="깨끗하게 찍은 사진일수록 정확해요! 📷"
    )

with col2:
    st.markdown("### ⚙️ 변형 옵션")
    difficulty = st.select_slider(
        "🎚️ 난이도 조절",
        options=["쉽게 🟢", "비슷하게 🟡", "어렵게 🔴"],
        value="비슷하게 🟡"
    )
    
    num_problems = st.slider(
        "🔢 생성할 문제 개수",
        min_value=1, max_value=5, value=3
    )
    
    include_solution = st.checkbox("📝 풀이 과정 포함", value=True)
    include_answer = st.checkbox("✅ 정답 포함", value=True)

# 이미지 표시
if uploaded_file is not None:
    image = Image.open(uploaded_file)
    
    st.markdown("---")
    st.markdown("### 🖼️ 업로드된 원본 문제")
    st.image(image, caption="📷 원본 수학 문제", use_container_width=True)
    
    st.markdown("---")
    
    # 변형 버튼
    if st.button("🚀 문제 변형하기!", use_container_width=True, type="primary"):
        
        # 난이도 텍스트 변환
        difficulty_map = {
            "쉽게 🟢": "원본보다 조금 더 쉬운 난이도로",
            "비슷하게 🟡": "원본과 비슷한 난이도로",
            "어렵게 🔴": "원본보다 조금 더 어려운 난이도로"
        }
        difficulty_text = difficulty_map[difficulty]
        
        # 옵션 텍스트
        solution_text = "각 문제마다 자세한 풀이 과정을 단계별로 보여주세요." if include_solution else ""
        answer_text = "각 문제의 최종 정답을 명확히 표시해주세요." if include_answer else ""
        
        # 프롬프트 작성
        prompt = f"""
당신은 친절하고 유능한 수학 선생님입니다. 🎓
업로드된 이미지에 있는 수학 문제를 분석하고, 같은 유형이지만 숫자나 조건을 변형한 새로운 문제 {num_problems}개를 만들어주세요.

📋 요구사항:
1. 먼저 원본 문제를 정확히 인식하고 분석해주세요.
2. 핵심 개념과 풀이 방법을 파악해주세요.
3. {difficulty_text} 새로운 문제 {num_problems}개를 만들어주세요.
4. 같은 수학적 개념과 유형을 유지하되, 숫자, 변수, 조건 등을 변형해주세요.
5. {solution_text}
6. {answer_text}

📝 응답 형식 (마크다운 사용):

## 🔍 원본 문제 분석
**문제 유형:** (예: 이차방정식의 근의 공식 활용)
**핵심 개념:** (사용된 수학 개념 설명)
**난이도:** ⭐⭐⭐ (별 1~5개)

---

## ✨ 변형 문제

### 📌 문제 1
(변형된 문제 내용)

{"**💡 풀이:**" if include_solution else ""}
{"(단계별 풀이 과정)" if include_solution else ""}

{"**✅ 정답:**" if include_answer else ""}
{"(최종 답)" if include_answer else ""}

---

(이런 형식으로 {num_problems}개 모두 작성)

## 🎯 학습 포인트
이 유형 문제를 풀 때 꼭 기억해야 할 핵심 팁을 알려주세요! 💪

수식은 LaTeX 형식으로 작성해주세요 (예: $x^2 + 2x + 1 = 0$).
한국어로 친절하게 설명해주세요. 😊
"""
        
        # AI 호출
        with st.spinner("🧠 AI가 문제를 분석하고 변형하는 중... ✨"):
            try:
                response = model.generate_content([prompt, image])
                result = response.text
                
                st.balloons()
                st.success("🎉 문제 변형 완료! 아래 결과를 확인하세요! 👇")
                
                st.markdown("---")
                st.markdown("## 📚 변형 결과")
                
                # 결과 카드로 표시
                st.markdown(f"""
                    <div class='problem-card'>
                        {result}
                    </div>
                """, unsafe_allow_html=True)
                
                # 실제 마크다운으로도 표시 (수식 렌더링용)
                with st.expander("📖 수식이 잘 안 보이면 여기를 클릭하세요!"):
                    st.markdown(result)
                
                # 다운로드 버튼
                st.markdown("---")
                st.download_button(
                    label="📥 변형 문제 다운로드 (txt)",
                    data=result,
                    file_name="변형문제.txt",
                    mime="text/plain",
                    use_container_width=True
                )
                
                # 학습 응원 메시지
                st.info("💪 이제 직접 풀어볼 시간이에요! 화이팅! 📝✏️")
                
            except Exception as e:
                st.error(f"❌ 오류가 발생했어요: {e}")
                st.warning("💡 API 키를 확인하거나, 이미지를 다시 업로드해보세요!")

else:
    # 안내 메시지
    st.markdown("---")
    st.markdown("""
    <div class='info-box'>
        <h3>🎯 이런 문제를 변형할 수 있어요!</h3>
        <ul>
            <li>📊 방정식, 부등식 문제</li>
            <li>📐 도형, 기하 문제</li>
            <li>📈 함수, 그래프 문제</li>
            <li>🎲 확률, 통계 문제</li>
            <li>🔢 수열, 미적분 문제</li>
            <li>➕ 사칙연산 문제까지!</li>
        </ul>
    </div>
    """, unsafe_allow_html=True)
    
    st.markdown("### 📸 위에서 문제 사진을 업로드해주세요!")

# 푸터
st.markdown("---")
st.markdown("""
    <div style='text-align: center; color: #888; padding: 20px;'>
        🎓 Made with 💙 by 당곡고 AI 도우미<br>
        <small>📚 스스로 풀어보는 것이 진짜 실력 향상의 길이에요! ✨</small>
    </div>
""", unsafe_allow_html=True)
