---
#
# Use the widgets beneath and the content will be
# inserted automagically in the webpage. To make
# this work, you have to use › layout: frontpage
#
layout: frontpage
header:
  image_fullwidth: header_unsplash_12.jpg
widget1:
  title: 'Blog'
  url: 'https://gebert.pl/blog/'
  image: widget-1-302x182.jpg
  text: 'Na moim blogu staram się dzielić przemyśleniami i wiedzą zdobytą podczas codziennej pracy. Opisuję tu zarówno tematy techniczne, jak i zagadnienia luźno związane z branżą IT.'
widget2:
  title: 'Współpraca'
  url: 'https://gebert.pl/contact/'
  text: 'W przypadku pytań, propozycji współpracy, czy dowolnych uwag, bardzo proszę o skorzystanie z danych przedstawionych w zakładce Kontakt.'
widget3:
  title: 'Doświadczenie zawodowe'
  url: 'https://www.linkedin.com/in/dominikgebert/'
  image: widget-github-303x182.jpg
  text: 'Moje doświadczenie zawodowe i CV dostępne jest w serwisie LinkedIn.'
#
# Use the call for action to show a button on the frontpage
#
# To make internal links, just use a permalink like this
# url: /getting-started/
#
# To style the button in different colors, use no value
# to use the main color or success, alert or secondary.
# To change colors see sass/_01_settings_colors.scss
#
#callforaction:
#  url: https://tinyletter.com/feeling-responsive
#  text: Inform me about new updates and features ›
#  style: alert
#permalink: /index.html
#
# This is a nasty hack to make the navigation highlight
# this page as active in the topbar navigation
#
homepage: true
