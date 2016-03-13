// ==UserScript==
// @name         Scrap.tf Entrada Em Raffles Automatica
// @namespace    http://scrap.tf
// @version      2.0
// @description  Entrada Automatica
// @author       MestreGamer
// @match        https://scrap.tf/*
// @grant        none
// @homepageURL  none
// @updateURL	 https://raw.githubusercontent.com/CYRIAQU3/ScrapTF-auto-raffle/master/autojoin.js
// @downloadURL  https://raw.githubusercontent.com/CYRIAQU3/ScrapTF-auto-raffle/master/autojoin.js
// ==/UserScript==

var openedTabs = 0;	//Numero de Abas Abertas
$(document).ready(function()
{
	scanHash();
});

function scanHash()
{
	var h = window.location.hash;
	var url = window.location.href;
	var u = "https://scrap.tf/raffles";

	if(url == u)
	{
		console.log("Scaneando Raffles...");
		scanRaffles();
		var sri = setInterval(function(){scanRaffles();},1000);
		setTimeout(function(){location.reload();},50000);	// reinicia a pagina depois de 50 seg
	}

	if (url.indexOf(u) > -1 && url != u)
	{
    	if(h == "#join")
		{
			console.log("Joining raffle...");
			joinRaffle();
		}
		else
		{
			window.close();
		}
	}
}

function scanRaffles()
{
	var pc = 0;
	var cId = "raffle-panel-1";
	var needScroll = true;
	var pl;
	var loadingDoneLabel = "That's all, no more!";


	pl = $(".pag-loading").text();
	
	if(pl == loadingDoneLabel)
	{
		needScroll = false;	// desativa scrool do mouse
	}

	if(needScroll)
	{
		$("html, body").animate({ scrollTop: $(document).height() }, 100);
	}

	$(".panel").each(function()
	{
		pc++;
		$(this).attr("id","raffle-panel-"+pc);
	});

	if(pc > 1)
	{
		$("#raffle-panel-1").hide();
		cId = "raffle-panel-2";
	}

	$("#"+cId).find(".panel-raffle").each(function()
	{
		var o = $(this).css("opacity");
		if(o == "0.6")
		{
			$(this).hide();
		}
		else
		{
			var r = $(this).attr("id");
			var raffleId = r.replace("raffle-box-","");
			if(openedTabs < 15)
			{
				var u = window.location.href+"/"+raffleId+"#join";
				var win = window.open(u, "", "width=0,height=0");
			    win.blur();
			  	win.resizeTo(0,0); 
			 	win.moveTo(0,window.screen.availHeight+10);
				openedTabs++;
				$(this).css("opacity","0.6").hide();
			}
		}
	});
}

function joinRaffle()
{
	window.opener.focus();
	var onclick = $("#raffle-enter").attr('onclick');
	eval("var sc = function(){"+onclick+"}");
	sc();
	window.location.hash = "#";
	setTimeout(function(){window.close();},5000);	// fecha paginas apos 5 seg
}
