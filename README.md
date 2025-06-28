# -AI-Powered-Learning
import gradio as gr
import pandas as pd
import matplotlib.pyplot as plt
from sklearn.linear_model import LogisticRegression
import openai
import random
import os

# 🔐 API Setup (Optional)
api_key = input("🔐 Enter OpenAI API key (press Enter to skip): ").strip()
client = openai.OpenAI(api_key=api_key) if api_key else None

# 🔁 Simulated Student History
student_data = {}

# 🎯 Career Track Recommender
def recommend_career_path(goal):
    paths = {
        "Data Scientist": "📘 Learn: Python, Statistics, SQL, Pandas, NumPy\n🤖 Advance: ML (Scikit-learn), Deep Learning (TensorFlow/PyTorch)\n📊 Practice: Kaggle, data projects",
        "Software Developer": "🧠 Learn: DSA, Java/C++, OOP\n🌐 Web Dev: HTML, CSS, JS\n🛠️ Projects + GitHub",
        "AI Researcher": "📘 Learn: DL, NLP, RL, research papers\n🧪 Tools: PyTorch, arXiv, OpenAI Gym\n📄 Try writing mini research papers",
        "Frontend Developer": "🎨 Learn: HTML, CSS, JS, React, Tailwind\n🧪 Projects: UI components, portfolios\n📐 Tools: Figma, Framer",
        "Backend Developer": "📘 Learn: Node.js, Express, APIs, DBs (MongoDB, SQL)\n⚙️ Practice Auth, JWT, CRUD APIs\n☁️ Deploy with Docker or Heroku",
        "UI/UX Designer": "🧠 Learn: Figma, design principles\n🧪 Skills: Wireframing, Prototyping, User Research\n🎨 Tools: Figma, Adobe XD, Maze",
        "Mobile App Developer": "📱 Learn: Dart, Flutter or Kotlin/Java\n🔗 Backend: Firebase, REST APIs\n🧪 Build: to-do apps, weather apps",
        "Game Developer": "🎮 Learn: Unity (C#), Game Physics, Animation\n🕹️ Practice: 2D/3D games\n🛠️ Tools: Unity, Unreal Engine",
        "Cybersecurity Analyst": "🔐 Learn: Networking, Linux, Cryptography\n🧪 Tools: Nmap, Wireshark, Metasploit\n🎓 Certs: CEH, CompTIA Sec+",
        "DevOps Engineer": "⚙️ Learn: CI/CD, Jenkins, Docker, K8s\n☁️ Cloud: AWS/GCP, Terraform\n📈 Monitor: Prometheus, Grafana",
        "Cloud Engineer": "☁️ Learn: AWS/GCP/Azure, Linux\n🧪 Tools: Terraform, Docker\n🎓 Certs: AWS Cloud Practitioner"
    }
    return paths.get(goal, "⚠️ Please select a valid career goal.")

# 🧠 Learning Style Detection
def detect_learning_style(q1, q2, q3, q4):
    responses = [q1, q2, q3, q4]
    visual = responses.count("A")
    reading = responses.count("B")
    kinesthetic = responses.count("C")
    if visual >= max(reading, kinesthetic): return "Visual"
    elif reading >= max(visual, kinesthetic): return "Reading"
    return "Kinesthetic"

# 📘 GPT-based Topic Explanation
def explain_topic(topic):
    if client:
        try:
            response = client.chat.completions.create(
                model="gpt-3.5-turbo",
                messages=[{"role": "user", "content": f"Explain {topic} in simple terms"}]
            )
            return response.choices[0].message.content
        except:
            return "(⚠️ API error. Showing offline explanation)"
    return f"(Offline) Basic intro to {topic}. Search on YouTube or W3Schools."

# 🎯 Suggest Next Step
def suggest_next_topic(score):
    if score < 50: return "Revise Basics – Level 1"
    elif score < 80: return "Go to Intermediate – Level 2"
    return "Advance to Complex Problems – Level 3"

# 📋 Feedback
def give_feedback(score, attendance, assignments, marks):
    fb = []
    if score < 50 or marks < 50: fb.append("⚠️ Focus on strengthening your basics.")
    elif score < 80: fb.append("🙂 Good progress, keep practicing.")
    else: fb.append("✅ Great job! Stay consistent.")
    if attendance < 60: fb.append("📅 Improve class attendance.")
    if assignments == 0: fb.append("📝 Try submitting your assignments.")
    tips = "\n📌 Tips: Use spaced repetition, visual tools, explain topics to others."
    return "\n".join(fb) + tips

# ✅ Performance Prediction Model
training_data = pd.DataFrame({
    'attendance': [90, 60, 75, 30],
    'assignments': [1, 0, 1, 0],
    'marks': [85, 40, 65, 20],
    'passed': [1, 0, 1, 0]
})
X = training_data[['attendance', 'assignments', 'marks']]
y = training_data['passed']
model = LogisticRegression()
model.fit(X, y)

def predict_performance(att, assign, mark):
    pred = model.predict([[att, assign, mark]])
    return "✅ Likely to Pass" if pred[0] == 1 else "⚠️ At Risk"

# 🗓️ Study Plan (Different topics each day)
def generate_study_plan(subject):
    day_topics = [
        "Fundamentals", "Key Concepts", "Case Study", "MCQs Practice",
        "Group Discussion", "Mini Project", "Summary & Flashcards"
    ]
    days = ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"]
    return "\n".join([f"📅 {d}: {subject} - {t}" for d, t in zip(days, day_topics)])

# 🏆 Badges + XP
def get_badges(score, attendance):
    badges = []
    if score > 90: badges.append("🏅 Concept Master")
    if attendance > 80: badges.append("📆 Consistency Champ")
    xp = score + attendance
    level = xp // 50
    return f"Badges: {', '.join(badges)}\nXP: {xp} | Level: {level}"

# 📈 Progress Graph
def generate_progress_plot(name):
    history = student_data.get(name, [(1, 40), (2, 50), (3, 60)])
    weeks = [h[0] for h in history]
    scores = [h[1] for h in history]
    plt.figure()
    plt.plot(weeks, scores, marker='o')
    plt.title(f"Progress of {name}")
    plt.xlabel("Week")
    plt.ylabel("Score")
    plt.grid(True)
    plt.tight_layout()
    path = "progress.png"  # ✅ Compatible with Windows
    plt.savefig(path)
    plt.close()
    return path

# 🎯 Daily Goal
def daily_goal_suggestion(subject):
    goals = [
        f"Watch 1 tutorial video on {subject}",
        f"Practice 3 questions from {subject}",
        f"Summarize notes of {subject} in your own words",
        f"Discuss a {subject} concept with a friend"
    ]
    return random.choice(goals)

# Final Demo
def full_demo(name, subject, score, attendance, assignments, marks, q1, q2, q3, q4, goal):
    style = detect_learning_style(q1, q2, q3, q4)
    topic = f"{subject} Basics"
    explanation = explain_topic(topic)
    next_step = suggest_next_topic(score)
    prediction = predict_performance(attendance, assignments, marks)
    feedback = give_feedback(score, attendance, assignments, marks)
    goal_suggestion = daily_goal_suggestion(subject)
    study_plan = generate_study_plan(subject)
    badges = get_badges(score, attendance)
    career = recommend_career_path(goal)

    # Update history
    history = student_data.get(name, [])
    history.append((len(history)+1, score))
    student_data[name] = history
    graph = generate_progress_plot(name)

    return (
        f"👤 Hello {name}!\n\n🧠 Learning Style: {style}\n\n"
        f"📊 Performance: {prediction}\n🔁 Next Step: {next_step}\n\n"
        f"🎓 Feedback:\n{feedback}\n\n"
        f"🎯 Daily Goal: {goal_suggestion}\n\n"
        f"🗓️ Weekly Study Plan:\n{study_plan}\n\n"
        f"🏆 {badges}\n\n"
        f"💼 Career Path:\n{career}\n\n"
        f"📘 Topic Explanation:\n{explanation}",
        graph
    )

# 🎛️ Gradio UI
demo = gr.Interface(
    fn=full_demo,
    inputs=[
        gr.Textbox(label="👤 Name"),
        gr.Dropdown(label="📚 Subject", choices=["Math", "Programming", "DBMS", "Operating Systems", "OOPs", "Cybersecurity", "AI"]),
        gr.Slider(0, 100, label="📊 Quiz Score (%)"),
        gr.Slider(0, 100, label="📅 Attendance (%)"),
        gr.Radio([0, 1], label="📝 Assignment Submitted? (0/1)"),
        gr.Slider(0, 100, label="🎯 Marks"),
        gr.Radio(["A", "B", "C"], label="Q1: (A=Video, B=Reading, C=Hands-on)"),
        gr.Radio(["A", "B", "C"], label="Q2: (A=Visuals, B=Text, C=Doing)"),
        gr.Radio(["A", "B", "C"], label="Q3: (A=YouTube, B=Books, C=Practice)"),
        gr.Radio(["A", "B", "C"], label="Q4: (A=Slides, B=Notes, C=Live Demo)"),
        gr.Dropdown(label="💼 Career Goal", choices=[
            "Data Scientist", "Software Developer", "AI Researcher",
            "Frontend Developer", "Backend Developer", "UI/UX Designer",
            "Mobile App Developer", "Game Developer", "Cybersecurity Analyst",
            "DevOps Engineer", "Cloud Engineer"
        ])
    ],
    outputs=["text", gr.Image(label="📈 Progress Graph")],
    title="🧠 AI-Powered Learning Assistant",
    description="Smart feedback, performance prediction, study plans, and goal-based advice for personalized student learning."
)

# 🚀 Launch
demo.launch(share=True)
