let scores = JSON.parse(localStorage.getItem('sleepScores') || '[]');

function calculateScore() {
    let score = 0;
    if (document.getElementById('phoneParked').checked) score++;
    if (document.getElementById('bedTime').checked) score++;
    if (document.getElementById('wakeTime').checked) score++;
    document.getElementById('todayScore').textContent = score + '/3';
    return score;
}

function updateStats() {
    const recentScores = scores.slice(-7);
    const average = recentScores.reduce((a, b) => a + b.score, 0) / (recentScores.length || 1);
    document.getElementById('weeklyAverage').textContent = average.toFixed(1);

    let streak = 0;
    for (let i = scores.length - 1; i >= 0; i--) {
        if (scores[i].score === 3) streak++;
        else break;
    }
    document.getElementById('currentStreak').textContent = streak + ' days';
}

function updateHistory() {
    const history = document.getElementById('scoreHistory');
    history.innerHTML = '';
    scores.slice().reverse().forEach(entry => {
        const div = document.createElement('div');
        div.className = 'history-entry';
        div.textContent = `${entry.date}: ${entry.score}/3 points`;
        history.appendChild(div);
    });
}

function saveScore() {
    const score = calculateScore();
    const today = new Date().toLocaleDateString();
    scores = scores.filter(s => s.date !== today);
    scores.push({ date: today, score: score });
    localStorage.setItem('sleepScores', JSON.stringify(scores));
    updateStats();
    updateHistory();
}

document.getElementById('phoneParked').addEventListener('change', calculateScore);
document.getElementById('bedTime').addEventListener('change', calculateScore);
document.getElementById('wakeTime').addEventListener('change', calculateScore);

updateStats();
updateHistory();
