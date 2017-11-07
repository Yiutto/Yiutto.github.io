---
title: mybooks
date: 2017-11-05 20:35:26
layout: 
comments: false
donate: false
---
{% iframe 'library.html' 100% 1900px %}


<script>
function setIframeHeight(iframe) {
	if (iframe) {
		var iframeWin = iframe.contentWindow || iframe.contentDocument.parentWindow;
		if (iframeWin.document.body) {
			iframe.height = iframeWin.document.documentElement.scrollHeight || iframeWin.document.body.scrollHeight;
		}
	}
};


setIframeHeight(document.getElementsByTagName('iframe')[0]);
window.onload = function () {
	setIframeHeight(document.getElementsByTagName('iframe')[0]);
};
</script>