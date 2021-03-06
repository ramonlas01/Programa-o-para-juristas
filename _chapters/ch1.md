---
title: "Capítulo 1: Expressões Regulares"
---

Nós começamos nossa exploração sobre programação com **expressões regulares**.[^1] Algumas vezes conhecida como "regex", as expressões regulares são uma forma de representar padrões de caracteres usando regras predefinidas. 

Pense em expressões regulares como uma versão mais rápida e mais poderosa do recurso "Localizar e Substituir" dos processadores de texto. Quando você pesquisa por algo como "Juiz" em um processador de texto, ele irá mostrar apenas combinações exatas - no máximo, você pode encontrar "juiz" também se desabilitar a diferenciação entre maísculas e minúsculas. Se você quer encontrar todas as combinações similares, como "judicial" e "judiciário", você terá que procurar manualmente por cada um deles. 

Se você já tiver acessado um mecanismo de busca de decisões judiciais como o do Supremo Tribunal Federal ou o do Superior Tribunal de Justiça, você provavelmente deve ter utilizado um operador lógico curinga como o "$" para lidar com esse tipo de tarefa. Mas, expressões regulares são *ainda mais poderosas* porque você pode dizer ao seu computador para encontrar muitos tipos diferentes de padrões que você esteja procurando, não apenas texto exato ou certos curingas predefinidos. Expressões regulares dá a você muito controle granular (mais possibilidades e níveis de controle) sobre seus padrões, e você pode até mesmo usar eles no [Microsoft Word](http://office.microsoft.com/en-us/word-help/find-and-replace-text-by-using-regular-expressions-advanced-HA102350661.aspx).

### Expressões Regulares

Regex pode ser algo intuitivo para muitos juristas, em especial para aqueles que estiveram envolvidos com Periódicos jurídicos.[^2] Isso porque referências e expressõres regulares possuem características similares. Por exemplo, advogados nos Estados Unidos sabem o que `5 U.S.C. § 552 (2006)` significa. Eles também sabem que `552 U.S. 5 (2007)` é uma referência completamente diferente. A primeira é uma Lei Federal promulgada pelo Congresso em 2006 e codificada no título 5, seção 552 do United States Code (USC). A última é uma decisão catalogada da Suprema Corte dos Estados Unidos, do ano de 2007, publicada no volume 552 do United States Reports, página 5. Mas, vamos examinar essas referências mais de perto.

Uma referência mínima ao United States Code tem quatro componentes principais: (1) O Título do United States Code que se está mencionando; (2) a abreviação "U.S.C. §"; (3) a seção especificada; e (4) a data aplicável (embora isso seja opcional). Assim, sabe-se que 42 U.S.C § 1983 é uma citação correta do United States Code, mas U.S.C. § 42-1983 não é. De maneira similar, uma referência mínima aos volumes do United States Reports também tem quatro componentes: (1) o número do volume do United States Reports; (2) a abreviação "U.S."; (3) a página inicial onde consta a decisão referida; e (4) a data de aplicação (e esse não é opcional).

(DIAGRAMA DE REFERÊNCIA DO U.S.C.)

O Regex nos permite montar padrões de maneira que um computador possa reconhecer esses padrões. Vamos ver como.

### A construção de blocos de regex

Sabe-se que até Maio de 2014, existiam 54 títulos do United States Code.[^3] Isso significa que nós podemos representar o primeiro componente de uma referência ora com um número de um digito (e.g., "8") ou com um número de dois dígitos ("18"). No regex, nós podemos representar qualquer número de um dígito como `[0-9]`. Isso significa que o Regex irá encontrar qualquer número entre 0 e 9. E quanto a *dois* dígitos? Bem, nós podemos escrever `[0-9][0-9]` e isso capturaria `15` e `25` por exemplo. Esse último regex, apresenta apenas um problema: ele iria rejeitar `8` em decorrência de `8` apenas possuir um único dígito.

Felizmente, o regex deixa você *escolher* a quantidade de dígitos que você quiser através da utilização de chaves `{ }`. Esse é um dos grandes pontos do regex: temos grande controle sobre o que estamos procurando. Podemos dizer ao regex que o número que queremos seja de um ou de dois dígitos da seguinte maneira: `[0-9]{1,2}`. Podemos quase tão facilmente dizer dizer que queremos um número de três dígitos: `[0-9]{1,3}`.[^4] Então, com `[0-9]{1,2}`, nós podemos representar um número de um ou de dois dígitos. Isso significa que podemos escrever `5`, `28` ou `51`, e o padrão irá encontrar *qualquer* um deles. Isso significa que qualquer título do United States Code seja encontrado.[^5] Nós acabamos de escrever nosso primeiro regex: `[0-9]{1,2}`. Bom trabalho!

Encorajado por esse sucesso, você pode estar avido por pular o "U.S. §" e ir direto para a própria seção específica, já que nós sabemos agora como representar números! Vamos acabar com essa ansiedade.

Almost as soon as we start, though, there's a problem. We don't know how many sections there are in any given title. For a given title, there may be as few as 146 sections, like in Title 4 ("Flag And Seal, Seat Of Government, And The States"), or there may many more, even up to § 18445 in Title 42 ("The Public Health And Welfare"). And what if Congress adds a new section? Would we have to go back and change our regex everytime a law is passed?

Happily, regex has a "shorthand" way of representing any single digit: `\d`. As a matter of semantics, then, `\d` shorthand is the same as `[0-9]`. If you repeat the shorthand (e.g., `\d\d`), you can represent any integer of a set number of digits. In other words, `\d\d` represents any two-digit number ([00-99]) and `\d\d\d` represents any three-digit number ([000-999]). And, importantly, you can even tell regex that you don't care *how many digits* using the plus sign (`+`). *Any positive integer* can be represented as follows: `\d+`.

You may be wondering why there is a backslash in the shorthand. This is a good, and important question. Regex accept literal characters: `d` is the letter "d", but `\d` is any integer between 0 and 9. In regex, the backslash has a special meaning and is called a "metacharacter". A backslash signals to regex that whatever comes next is something to pay attention to. Similarly, the plus sign that we used before (`\d+`) is a metacharacter. The plus sign signals to regex that you can repeat the preceding regex group.

In all, regex recognizes 12 metacharacters[^6], though describing all of them is beyond the scope of this chapter. For now, it is merely important to acknowledge that the period (`.`) and parentheses (`(` and `)`) are metacharacters too.

The period (in regex, a "dot") can represent *any* character. A regex in a parentheses is called a "group." If we want to actually represent a metacharacter in the regex, we use a backslash to "escape" the metacharacter. In other words, if we want to use a period at the end of a sentence, we need to write `\.`, and if we want to use a backslash in our pattern, we need to write `\\`.

By now, you already know a great deal about basic regex. For example, what if we wanted to represent "552a" or "2000aa"? We could do this by writing `\d+a+`. This would also match "1a" and "1234aaaa". And if wanted to do "2000bb", we could just change our regex to be as follows: `\d+[a-z]+`. This would represent "1z" and "12345abcde".

Regex also allows for the shorthand `\w`, which represents any single alphanumeric character. *Any word* can be represented by `\w+`. So, you could simply write the regular expression as `\d+\w+`. Think of `\w` as the ultimate Scrabble piece. Potent stuff.

But, what if you wanted your pattern to match "552a" *and* "552". In other words, you don't *necessarily* want any letters to come after the numbers. To do this, the question mark metacharacter (`?`) comes into play. Here, you could simply write `552a?`, which means that the "a" is optional. By using parentheses for grouping, you can supercharge the use of the `?`. For example, you could write `(\d+)(\w+)?`, which would cover "552", "552a", and "2000bb". By grouping the repeating alphanumeric characters represented by `\w` and putting a question mark afterward, you can basically make all of the letters optional.

### Assembling the United States Code

Believe it or not, we know everything to represent the minimal citation to a section in the United States Code (and even more complicated ones!). Let's return to the 4 minimum components of the citation:

(DIAGRAM OF U.S.C. CITATION)

First, there is the title: `[0-9]{1,2}`. Second there is the "U.S.C. §", which requires some escaping of the periods but is written as follows: `U\.S\.C\. §`. Third, there is the section: `\d+(\w+)?`.[^7] And finally, there is the date: `[0-9]{1,2}`[^8]. As a reminder for the date, before we put it all together: the date is optional.

Now, let's put together the citation:

`[0-9]{1,2} U\.S\.C\. § \d+(\w+)?( \([0-9]{4}\))?`

Congratulations. You have constructed a relatively complex regex! And there's more good news, the road gets easier from here.

### Atividades

1. Practice with a website: A few good examples are [rubular](http://www.rubular.com/),the more feature-filled [regex 101](http://regex101.com/) and the famous [RegExr](http://www.regexr.com/).

2. Extend the regular expression to capture subsections and paragraphs.

3. Write a regular expression to capture a simple email address.

***

### Notas de Rodapé

[^1]: Many programmers would, I suspect, suggest that this is a strange place to begin. They would argue that it is not really even *coding*. It is my view that this is a proper subject for a beginning chapter. *Cf.* *Brown v. Allen*, 344 U.S. 443, 540 (1953) (Jackson, J., concurring) ("There is no doubt that if there were a super-Supreme Court, a substantial proportion of our reversals of state courts would also be reversed. We are not final because we are infallible, but we are infallible only because we are final."). Plus, by the time you are done with this chapter, you can impress your coding friends that you know basic "regex". A word of caution, the universe of non-coders who will be impressed your knowledge of regex is rather small.

[^2]: A quick review of my curriculum vitae demonstrates that I was not on law review in law school. I am using many footnotes to compensate.

[^3]: Editorial Reclassification, Title 52, United States Code, *available online at* [http://uscode.house.gov/editorialreclassification/t52/index.html](http://uscode.house.gov/editorialreclassification/t52/index.html).

[^4]: If you're so inclined, you can even tell regex to match any four-letter word: `[a-zA-Z]{4}`.

[^5]: Admittedly, it would capture `99` too, but let's not let the perfect be the enemy of the good. See what I mean about the footnotes? *See supra* n. 2.

[^6]: List of all twelve metacharacters. "the backslash `\`, the caret `^`, the dollar sign `$`, the period or dot `.`, the vertical bar or pipe symbol `|`, the question mark `?`, the asterisk or star `*`, the plus sign `+`, the opening parenthesis `(`, the closing parenthesis `)`, the opening square bracket `[`, and the opening curly brace `{`." Source: http://www.regular-expressions.info/quickstart.html

[^7]: A different, and more typical, way of writing this would be to use the asterisk `*` metacharacter. Using `*` matches whatever precedes it zero or more times. Accordingly, `.*` matches "a" or "abc123" or even nothing at all. Ultimately, then, the pattern `\d+(\w+)?` could also have been written as `\d+\w*`.

[^8]: There are multiple candidates for representing the date. Another might be `\d\d\d\d` or `\d{4}`. I took the license to demonstrate that you can mix and match.
