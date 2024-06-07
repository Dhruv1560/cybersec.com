<!doctype html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Personal Portfolio &mdash; Powered by Behance</title>
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<meta name="description" content="A simple single page to show your portfolio, powered by Behance API and a number of modern web tools">

	<link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/normalize/3.0.1/normalize.min.css">
	<link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/magnific-popup.js/0.9.9/magnific-popup.css">
	<link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/foundicons/3.0.0/foundation-icons.min.css">
	<link href='http://fonts.googleapis.com/css?family=Cantata+One|Open+Sans:300,600' rel='stylesheet' type='text/css'>
	<link rel="stylesheet" href="css/style.css">

	<script src="//cdnjs.cloudflare.com/ajax/libs/jquery/1.11.0/jquery.min.js"></script>
	<script src="//cdnjs.cloudflare.com/ajax/libs/handlebars.js/1.3.0/handlebars.min.js"></script>
	<script src="//cdnjs.cloudflare.com/ajax/libs/magnific-popup.js/0.9.9/jquery.magnific-popup.min.js"></script>
</head>
<body>
	<header id="header" class="portfolio-header clearfix">
	<script id="profile-template" type="text/x-handlebars-template">
		<figure class="profile-avatar"><img src="{{user.images.[138]}}" alt=""></figure>
		<h1 class="profile-name">{{user.display_name}}</h1>
		<div class="profile-fields">
			<ul class="field-list">
				{{#each user.fields}}
					<li class="field-item">{{this}}</li>
				{{/each}}
			</ul>
		</div>
		<div class="profile-location fi-marker">{{user.city}}, {{user.country}}</div>
	</script>
	</header>
	<div id="content" class="content-area clearfix">
		<div id="portfolio" class="portfolio-area clearfix">		
		<script id="portfolio-template" type="text/x-handlebars-template">
		<ul class="portfolio-list clearfix">
			{{#each projects}}
			<li class="portfolio-item">
				<div class="portfolio-content">
					<figure class="portfolio-cover" title="{{this.name}}" data-project-id="{{this.id}}">
						{{#if this.covers.[404]}}
						<img class="portfolio-image" src="{{this.covers.[404]}}" alt="">
						{{else}}
							{{#if this.covers.[230]}}
							<img class="portfolio-image" src="{{this.covers.[230]}}" alt="">
							{{else}}
							<img class="portfolio-image" src="{{this.covers.[202]}}" alt="">
							{{/if}}
						{{/if}}
					</figure>
					<h2 class="portfolio-title">{{this.name}}</h2>
					<div class="portfolio-fields">
						<ul class="field-list">
						{{#each this.fields}}
							<li class="field-item">{{this}}</li>
						{{/each}}
						</ul>
					</div>
				</div>
			</li>
			{{/each}}
		</ul>
		</script>
		</div>
	</div>
	<footer id="footer" class="portfolio-footer clearfix">
		<div id="power" class="power-by">
			<p>Powered by</p>
			<p><a class="power-logo fi-social-behance" href="http://www.behance.net/" title="Behance" target="_blank">Behance</a></p>
		</div>
	</footer>
	
	<a href="https://github.com/creatiface/personal-portfolio"><img style="position: absolute; top: 0; right: 0; border: 0;" src="https://camo.githubusercontent.com/652c5b9acfaddf3a9c326fa6bde407b87f7be0f4/68747470733a2f2f73332e616d617a6f6e6177732e636f6d2f6769746875622f726962626f6e732f666f726b6d655f72696768745f6f72616e67655f6666373630302e706e67" alt="Fork me on GitHub" data-canonical-src="https://s3.amazonaws.com/github/ribbons/forkme_right_orange_ff7600.png"></a>

	<script>
	$(document).ready(function() {

		var apiKey  = 'ZLBxK9rEfHwJf9K0rmseNr2fS2gS2HJW';
		var userID  = 'creativemints';

		(function() {
			var behanceUserAPI = 'http://www.behance.net/v2/users/'+ userID +'?callback=?&api_key='+ apiKey;

			function setUserTemplate() {
				var userData     = JSON.parse(sessionStorage.getItem('behanceUser')),
					getTemplate = $('#profile-template').html(),
					template     = Handlebars.compile(getTemplate),
					result       = template(userData);
			    $('#header').html(result);
			};

			if(sessionStorage.getItem('behanceUser')) {
				setUserTemplate();
			} else {
				$.getJSON(behanceUserAPI, function(user) {
					var data = JSON.stringify(user);
					sessionStorage.setItem('behanceUser', data);
					setUserTemplate();
				});
			};
		})();

		(function() {
			var perPage = 12;
			var behanceProjectAPI = 'http://www.behance.net/v2/users/' + userID + '/projects?callback=?&api_key=' + apiKey + '&per_page=' + perPage;

			function setPortfolioTemplate() {
				var projectData = JSON.parse(sessionStorage.getItem('behanceProject')),
					getTemplate = $('#portfolio-template').html(),
					template    = Handlebars.compile(getTemplate),
					result      = template(projectData);
				$('#portfolio').html(result);
			};

			if(sessionStorage.getItem('behanceProject')) {
				setPortfolioTemplate();
			} else {
				$.getJSON(behanceProjectAPI, function(project) {
					var data = JSON.stringify(project);
					sessionStorage.setItem('behanceProject', data);
					setPortfolioTemplate();
				});
			};
		})();

		$('#portfolio').on('click', '.portfolio-cover', function() {
			var $this =	$(this),
				projectID = $this.data('project-id'),
				beProjectContentAPI = 'http://www.behance.net/v2/projects/'+ projectID +'?callback=?&api_key=' + apiKey,
				keyName = 'behanceProjectImages-' + projectID;
			
			function showGallery(dataSource) {	
				$this.magnificPopup({
					items: dataSource,
					gallery: {
						enabled: true
					},
					type: 'image',
					mainClass: 'animated',
					removalDelay: 350
				}).magnificPopup('open');
			};

			if(localStorage.getItem(keyName)) {
				var srcItems = JSON.parse(localStorage.getItem(keyName));
				showGallery(srcItems);
			} else {
				$.getJSON(beProjectContentAPI, function(projectContent) {
					var src = [];
					$.each(projectContent.project.modules, function(index, mod) {
						if(mod.src != undefined) {
							src.push({ src: mod.src });	
						}
					});
					showGallery(src);
					var data = JSON.stringify(src);
					localStorage.setItem(keyName, data);
				});
			};
		});
	});
	</script>
</body>
</html>	
