# Jack-Cashman
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8">
	<title>Horizon</title>
	<style>
		body {
			background-color: #F0F0F0;
		}
		.container {
			display: flex;
			flex-direction: column;
			align-items: center;
			margin-top: 50px;
		}
		#title {
			font-size: 50px;
			font-weight: bold;
			color: black;
			margin-bottom: 30px;
		}
		#search-box {
			display: flex;
			align-items: center;
			background-color: white;
			padding: 5px;
			border-radius: 14px;
			box-shadow: 0px 0px 5px rgba(0, 0, 0, 0.3);
		}
		#search-input {
			flex-grow: 1;
			border: none;
			font-size: 20px;
			padding: 5px;
			margin-left: 10px;
			outline: none;
		}
		#search-input:focus {
			border: none;
			outline: none;
		}
		#search-button {
			background-color: #2196F3;
			color: white;
			border: none;
			font-size: 20px;
			border-radius: 10px;
			padding: 5px 10px;
			margin-left: 10px;
			cursor: pointer;
		}
		#result-box {
			background-color: white;
			padding: 20px;
			border-radius: 14px;
			box-shadow: 0px 0px 5px rgba(0, 0, 0, 0.3);
			margin-top: 30px;
			width: 500px;
			height: 280px;
			overflow: auto;
		}
		#reveal-button {
			background-color: #2196F3;
			color: white;
			border: none;
			font-size: 20px;
			border-radius: 10px;
			padding: 5px 10px;
			margin-left: 10px;
			cursor: pointer;
			float: right;
		}
		#reveal-button:hover {
			background-color: #0D47A1;
		}
	</style>
</head>
<body>
	<div class="container">
		<div id="title">Horizon</div>
		<div id="search-box">
			<img src="wikipedia.png" width="28px" height="28px" style="margin-left: 10px;">
			<input type="text" id="search-input" placeholder="Search on Wikipedia...">
			<button id="search-button">Search</button>
		</div>
		<div id="result-box"></div>
		<button id="reveal-button">Reveal</button>
	</div>
	<script src="https://cdn.jsdelivr.net/npm/@openai/api@0.6.0/dist/openai-api.min.js"></script>
	<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
	<script>
		const openai = new OpenAI("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");

		$("#search-button").on("click", async function() {
			const query = $("#
def search(query):
    wiki = wikipediaapi.Wikipedia('en')
    page = wiki.page(query)
    if page.exists():
        summary = page.summary
        text = summary.split('\n')[0]
        source = f'Source: {page.fullurl}'
        return text, source
    else:
        return 'Sorry, no results were found for your query.', ''

def get_answers(query):
    text, source = search(query)
    prompt = f"What are some questions related to {query}?"
    inputs = tokenizer(prompt, return_tensors='pt')
    outputs = model.generate(inputs['input_ids'], max_length=128, num_return_sequences=3, early_stopping=True)
    questions = [tokenizer.decode(output, skip_special_tokens=True) for output in outputs]
    return text, source, questions

def update_results():
    query = search_box.value
    text, source, questions = get_answers(query)
    result_box.value = f'{text}\n\n{source}\n\nSome related questions:\n' + '\n'.join([f'{i+1}. {q}' for i,q in enumerate(questions)])

<input type="text" id="search-input" placeholder="输入搜索关键词...">
<div id="suggestion-box"></div>

<script>
const input = document.querySelector('#search-input');
const suggestionBox = document.querySelector('#suggestion-box');

// 监听输入框输入
input.addEventListener('input', async () => {
  const keyword = input.value.trim();

  if (keyword === '') {
    suggestionBox.innerHTML = '';
    return;
  }

  // 发送Ajax请求
  const res = await fetch(`/suggestion?keyword=${keyword}`);
  const suggestions = await res.json();

  // 渲染搜索建议列表
  suggestionBox.innerHTML = '';
  suggestions.forEach(suggestion => {
    const div = document.createElement('div');
    div.textContent = suggestion;
    suggestionBox.appendChild(div);
  });
});
</script>

@app.route('/suggestion')
def suggestion():
    keyword = request.args.get('keyword')
    suggestions = get_suggestions(keyword)
    return jsonify(suggestions)

def get_suggestions(keyword):
    # 在数据库中查找与输入词相关的建议
    suggestions = db.execute(
        'SELECT keyword FROM search_history WHERE keyword LIKE ? LIMIT 10',
        (f'%{keyword}%',)
    ).fetchall()

    # 将结果转换为字符串列表
    suggestions = [row['keyword'] for row in suggestions]

    return suggestions

<div id="hot-search">
  <div class="title">热门搜索</div>
  <ul>
    {% for keyword in hot_keywords %}
    <li><a href="/search?q={{ keyword }}">{{ keyword }}</a></li>
    {% endfor %}
  </ul>
</div>

def get_hot_keywords():
    # 在数据库中查找最近一段时间内搜索量最大的关键词
    hot_keywords = db.execute(
        'SELECT keyword, COUNT(*) AS cnt FROM search_history WHERE timestamp > ? GROUP BY keyword ORDER BY cnt DESC LIMIT 10',
        (time.time() - 7 * 86400,)
    ).fetchall()

    # 将结果转换为关键词列表
    hot_keywords = [row['keyword'] for row in hot_keywords]

    return hot_keywords

@app.route('/history')
def history():
    # 在数据库中查找最近一段时间内搜索历史记录
    history = db.execute(
        'SELECT keyword, timestamp FROM search_history WHERE timestamp > ? ORDER BY timestamp DESC LIMIT 10',
        (time.time() - 7 * 86400,)
    ).fetchall

