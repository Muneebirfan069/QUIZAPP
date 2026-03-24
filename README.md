#QUIZNUMBER APP
#APP WITH MULTIPLE QUESTIONS HAVING A TIMER OF THREE SECONDS AND A MESSAGE OF TIME EXPIRED

import 'dart:async';
import 'package:flutter/material.dart';

void main() {
  runApp(QuizApp());
}

class QuizApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: QuizScreen(),
    );
  }
}

class QuizScreen extends StatefulWidget {
  @override
  _QuizScreenState createState() => _QuizScreenState();
}

class _QuizScreenState extends State<QuizScreen> {
  int questionIndex = 0;
  int score = 0;
  bool answered = false;

  final int totalTime = 3;   // 🔥 3 Second Timer
  int timeLeft = 3;

  Timer? _timer;

  List<Map<String, dynamic>> questions = [
    {
      "question": "What is 5 + 3 ?",
      "options": ["6", "7", "8", "9"],
      "answer": "8"
    },
    {
      "question": "What is 10 - 4 ?",
      "options": ["5", "6", "7", "8"],
      "answer": "6"
    },
    {
      "question": "What is 6 × 2 ?",
      "options": ["10", "11", "12", "13"],
      "answer": "12"
    },
    {
      "question": "What is 20 ÷ 4 ?",
      "options": ["3", "4", "5", "6"],
      "answer": "5"
    },
  ];

  @override
  void initState() {
    super.initState();
    startTimer();
  }

  void startTimer() {
    timeLeft = totalTime;
    _timer?.cancel();

    _timer = Timer.periodic(Duration(seconds: 1), (timer) {
      if (timeLeft > 0) {
        setState(() {
          timeLeft--;
        });
      } else {
        timer.cancel();

        // Show Time Expired Message
        ScaffoldMessenger.of(context).showSnackBar(
          SnackBar(
            backgroundColor: Colors.orange,
            content: Text(
              "⏰ Time is Expired!",
              style: TextStyle(fontSize: 18),
            ),
            duration: Duration(seconds: 1),
          ),
        );

        Future.delayed(Duration(seconds: 1), () {
          nextQuestion();
        });
      }
    });
  }

  void nextQuestion() {
    setState(() {
      answered = false;

      if (questionIndex < questions.length - 1) {
        questionIndex++;
        startTimer();
      } else {
        Navigator.pushReplacement(
          context,
          MaterialPageRoute(
            builder: (_) => ResultScreen(score: score),
          ),
        );
      }
    });
  }

  void checkAnswer(String selectedOption) {
    if (answered) return;

    setState(() {
      answered = true;
    });

    bool isCorrect =
        selectedOption == questions[questionIndex]["answer"];

    if (isCorrect) score++;

    _timer?.cancel();

    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(
        backgroundColor: isCorrect ? Colors.green : Colors.red,
        content: Text(
          isCorrect ? "Correct Answer 🎉" : "Wrong Answer ❌",
          style: TextStyle(fontSize: 18),
        ),
        duration: Duration(seconds: 1),
      ),
    );

    Future.delayed(Duration(seconds: 1), () {
      nextQuestion();
    });
  }

  @override
  void dispose() {
    _timer?.cancel();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    double progress =
        (questionIndex + 1) / questions.length;

    return Scaffold(
      appBar: AppBar(
        title: Text("🔥 3s Timed Quiz"),
        backgroundColor: Colors.deepPurple,
      ),
      body: Container(
        decoration: BoxDecoration(
          gradient: LinearGradient(
            colors: [Colors.deepPurple, Colors.blueAccent],
            begin: Alignment.topLeft,
            end: Alignment.bottomRight,
          ),
        ),
        child: Padding(
          padding: const EdgeInsets.all(20),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [

              // Quiz Progress
              LinearProgressIndicator(
                value: progress,
                backgroundColor: Colors.white30,
                color: Colors.yellow,
                minHeight: 8,
              ),

              SizedBox(height: 20),

              // Timer Text
              Text(
                "Time Left: $timeLeft s",
                style: TextStyle(
                  fontSize: 20,
                  color: Colors.white,
                  fontWeight: FontWeight.bold,
                ),
              ),

              SizedBox(height: 10),

              // Timer Progress Bar
              LinearProgressIndicator(
                value: timeLeft / totalTime,
                backgroundColor: Colors.white30,
                color: Colors.red,
                minHeight: 8,
              ),

              SizedBox(height: 30),

              // Question Card
              Card(
                shape: RoundedRectangleBorder(
                  borderRadius: BorderRadius.circular(20),
                ),
                elevation: 10,
                child: Padding(
                  padding: const EdgeInsets.all(20),
                  child: Text(
                    questions[questionIndex]["question"],
                    style: TextStyle(
                      fontSize: 22,
                      fontWeight: FontWeight.bold,
                    ),
                    textAlign: TextAlign.center,
                  ),
                ),
              ),

              SizedBox(height: 30),

              // Options
              ...questions[questionIndex]["options"]
                  .map<Widget>((option) {
                return Container(
                  width: double.infinity,
                  margin: EdgeInsets.symmetric(vertical: 8),
                  child: ElevatedButton(
                    style: ElevatedButton.styleFrom(
                      padding: EdgeInsets.all(15),
                      backgroundColor: Colors.orangeAccent,
                      shape: RoundedRectangleBorder(
                        borderRadius:
                        BorderRadius.circular(15),
                      ),
                    ),
                    onPressed: () => checkAnswer(option),
                    child: Text(
                      option,
                      style: TextStyle(
                          fontSize: 18,
                          color: Colors.black),
                    ),
                  ),
                );
              }).toList(),
            ],
          ),
        ),
      ),
    );
  }
}

class ResultScreen extends StatelessWidget {
  final int score;

  ResultScreen({required this.score});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.deepPurple,
      body: Center(
        child: Card(
          shape: RoundedRectangleBorder(
            borderRadius: BorderRadius.circular(25),
          ),
          elevation: 15,
          child: Padding(
            padding: const EdgeInsets.all(30),
            child: Column(
              mainAxisSize: MainAxisSize.min,
              children: [
                Text(
                  "🏆 Quiz Completed!",
                  style: TextStyle(
                    fontSize: 24,
                    fontWeight: FontWeight.bold,
                  ),
                ),
                SizedBox(height: 20),
                Text(
                  "Your Score: $score",
                  style: TextStyle(
                    fontSize: 22,
                    color: Colors.green,
                    fontWeight: FontWeight.bold,
                  ),
                ),
                SizedBox(height: 20),
                ElevatedButton(
                  style: ElevatedButton.styleFrom(
                    backgroundColor: Colors.red,
                    padding: EdgeInsets.symmetric(
                        horizontal: 30, vertical: 12),
                    shape: RoundedRectangleBorder(
                      borderRadius:
                      BorderRadius.circular(15),
                    ),
                  ),
                  onPressed: () {
                    Navigator.pushReplacement(
                      context,
                      MaterialPageRoute(
                        builder: (_) => QuizScreen(),
                      ),
                    );
                  },
                  child: Text(
                    "Restart",
                    style: TextStyle(color: Colors.white),
                  ),
                )
              ],
            ),
          ),
        ),
      ),
    );
  }
}
### Output Screenshots

![Result Screen](qscr1.png/qscr2.png)
