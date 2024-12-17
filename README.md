# django-quiz-app
Code Implementation
1. Models (models.py)
from django.db import models

class Question(models.Model):
    question_text = models.TextField()
    option_a = models.CharField(max_length=255)
    option_b = models.CharField(max_length=255)
    option_c = models.CharField(max_length=255)
    option_d = models.CharField(max_length=255)
    correct_option = models.CharField(max_length=1, choices=[('A', 'A'), ('B', 'B'), ('C', 'C'), ('D', 'D')])

    def __str__(self):
        return self.question_text

class QuizResult(models.Model):
    total_questions = models.IntegerField(default=0)
    correct_answers = models.IntegerField(default=0)
    incorrect_answers = models.IntegerField(default=0)

2. Forms (forms.py)
from django import forms

class AnswerForm(forms.Form):
    answer = forms.ChoiceField(choices=[('A', 'A'), ('B', 'B'), ('C', 'C'), ('D', 'D')], widget=forms.RadioSelect)

3. Views (views.py)
from django.shortcuts import render, redirect
from django.http import JsonResponse
from .models import Question, QuizResult
from .forms import AnswerForm
import random

def start_quiz(request):
    # Initialize quiz session
    QuizResult.objects.all().delete()
    QuizResult.objects.create(total_questions=0, correct_answers=0, incorrect_answers=0)
    return redirect('get_question')

def get_question(request):
    # Get a random question
    question = random.choice(Question.objects.all())
    form = AnswerForm()
    return render(request, 'quiz/question.html', {'question': question, 'form': form})

def submit_answer(request, question_id):
    question = Question.objects.get(id=question_id)
    form = AnswerForm(request.POST)
    result = QuizResult.objects.first()

    if form.is_valid():
        result.total_questions += 1
        if form.cleaned_data['answer'] == question.correct_option:
            result.correct_answers += 1
        else:
            result.incorrect_answers += 1
        result.save()
    return redirect('get_results')

def get_results(request):
    result = QuizResult.objects.first()
    return render(request, 'quiz/results.html', {'result': result})

4. URLs (urls.py)
from django.urls import path
from . import views

urlpatterns = [
    path('start/', views.start_quiz, name='start_quiz'),
    path('question/', views.get_question, name='get_question'),
    path('submit/<int:question_id>/', views.submit_answer, name='submit_answer'),
    path('results/', views.get_results, name='get_results'),
]

5. HTML Templates (templates/quiz/)
a. question.html
<!DOCTYPE html>
<html>
<head>
    <title>Quiz Question</title>
</head>
<body>
    <h1>{{ question.question_text }}</h1>
    <form method="post" action="{% url 'submit_answer' question.id %}">
        {% csrf_token %}
        {{ form.as_p }}
        <button type="submit">Submit</button>
    </form>
</body>
</html>

b. results.html
<!DOCTYPE html>
<html>
<head>
    <title>Quiz Results</title>
</head>
<body>
    <h1>Quiz Results</h1>
    <p>Total Questions Answered: {{ result.total_questions }}</p>
    <p>Correct Answers: {{ result.correct_answers }}</p>
    <p>Incorrect Answers: {{ result.incorrect_answers }}</p>
    <a href="{% url 'start_quiz' %}">Start New Quiz</a>
</body>
</html>



Here’s a Django-based implementation for a Quiz App as described.

Key Features:
Start a New Quiz Session: A user can initiate a new session.
Retrieve Random Questions: Fetch a random multiple-choice question from a pre-seeded database.
Submit Answer: Submit an answer and check if it’s correct.
View Results: Display the number of answered questions with details of correct and incorrect submissions.
Assumptions (to be included in README.md):
Only one user interacts with the system.
The database is pre-populated with multiple-choice questions.
Each question has one correct answer.
Project Structure:
plaintext
Copy code
quiz_app/
├── quiz/                    # Django app
│   ├── migrations/          # Database migrations
│   ├── static/              # Static files for CSS/JS
│   ├── templates/           # HTML templates
│   ├── models.py            # Models for questions and results
│   ├── views.py             # Core application logic
│   ├── urls.py              # App-specific URLs
│   ├── forms.py             # Django forms for user submissions
│   └── admin.py             # Admin configuration
├── manage.py                # Django management commands
└── db.sqlite3               # SQLite database
Code Implementation
1. Models (models.py)
python
Copy code
from django.db import models

class Question(models.Model):
    question_text = models.TextField()
    option_a = models.CharField(max_length=255)
    option_b = models.CharField(max_length=255)
    option_c = models.CharField(max_length=255)
    option_d = models.CharField(max_length=255)
    correct_option = models.CharField(max_length=1, choices=[('A', 'A'), ('B', 'B'), ('C', 'C'), ('D', 'D')])

    def __str__(self):
        return self.question_text

class QuizResult(models.Model):
    total_questions = models.IntegerField(default=0)
    correct_answers = models.IntegerField(default=0)
    incorrect_answers = models.IntegerField(default=0)
2. Forms (forms.py)
python
Copy code
from django import forms

class AnswerForm(forms.Form):
    answer = forms.ChoiceField(choices=[('A', 'A'), ('B', 'B'), ('C', 'C'), ('D', 'D')], widget=forms.RadioSelect)
3. Views (views.py)
python
Copy code
from django.shortcuts import render, redirect
from django.http import JsonResponse
from .models import Question, QuizResult
from .forms import AnswerForm
import random

def start_quiz(request):
    # Initialize quiz session
    QuizResult.objects.all().delete()
    QuizResult.objects.create(total_questions=0, correct_answers=0, incorrect_answers=0)
    return redirect('get_question')

def get_question(request):
    # Get a random question
    question = random.choice(Question.objects.all())
    form = AnswerForm()
    return render(request, 'quiz/question.html', {'question': question, 'form': form})

def submit_answer(request, question_id):
    question = Question.objects.get(id=question_id)
    form = AnswerForm(request.POST)
    result = QuizResult.objects.first()

    if form.is_valid():
        result.total_questions += 1
        if form.cleaned_data['answer'] == question.correct_option:
            result.correct_answers += 1
        else:
            result.incorrect_answers += 1
        result.save()
    return redirect('get_results')

def get_results(request):
    result = QuizResult.objects.first()
    return render(request, 'quiz/results.html', {'result': result})
4. URLs (urls.py)
python
Copy code
from django.urls import path
from . import views

urlpatterns = [
    path('start/', views.start_quiz, name='start_quiz'),
    path('question/', views.get_question, name='get_question'),
    path('submit/<int:question_id>/', views.submit_answer, name='submit_answer'),
    path('results/', views.get_results, name='get_results'),
]
5. HTML Templates (templates/quiz/)
a. question.html

html
Copy code
<!DOCTYPE html>
<html>
<head>
    <title>Quiz Question</title>
</head>
<body>
    <h1>{{ question.question_text }}</h1>
    <form method="post" action="{% url 'submit_answer' question.id %}">
        {% csrf_token %}
        {{ form.as_p }}
        <button type="submit">Submit</button>
    </form>
</body>
</html>
b. results.html

html
Copy code
<!DOCTYPE html>
<html>
<head>
    <title>Quiz Results</title>
</head>
<body>
    <h1>Quiz Results</h1>
    <p>Total Questions Answered: {{ result.total_questions }}</p>
    <p>Correct Answers: {{ result.correct_answers }}</p>
    <p>Incorrect Answers: {{ result.incorrect_answers }}</p>
    <a href="{% url 'start_quiz' %}">Start New Quiz</a>
</body>
</html>
Seed Database with Questions (manage.py shell)
from quiz.models import Question

questions = [
    {
        "question_text": "What is the capital of France?",
        "option_a": "Berlin",
        "option_b": "Madrid",
        "option_c": "Paris",
        "option_d": "Rome",
        "correct_option": "C"
    },
    {
        "question_text": "Which planet is known as the Red Planet?",
        "option_a": "Earth",
        "option_b": "Mars",
        "option_c": "Venus",
        "option_d": "Jupiter",
        "correct_option": "B"
    }
]

for q in questions:
    Question.objects.create(**q)

Run the Application
Start the server:
python manage.py runserver

Access the app at http://127.0.0.1:8000/quiz/start/.
