---
title: Methodik Hate Speech Classifier
---

Ich habe ein Machine Learning Modell entwickelt, das Hate Speech in Textnachrichten erkennen kann, einen Hate Speech Classifier. Der ganze Programmiercode für das entwickelte Modell kann auf Github abgerufen werden. 

[github.com/pscllbssr/hatespeech-ml](https://github.com/pscllbssr/hatespeech-ml)

Nachfolgend eine Beschreibung, wie ich konkret vorgegangen bin, um das Modell zu entwickeln.

## Datenbeschaffung und -aufbereitung

Damit das Modell lernen konnte, Hasskommentare zu erkennen, benötigte ich zuerst genügend Trainingsdaten. Also Beispiele von Hasskommenteren, aber auch Gegenbeispiele, Kommentare frei von Hass. 

Zu diesem Zweck wollte ich ursprünglich Hasskommentare verwenden, bei denen es zu einer Verurteilung kam. Ein solcher Datensatz war aber schwer zu beschaffen und in der Summe zu wenige Beispiele, um Machine Learning damit zu betreiben. Ein anderer Ansatz war, Kommentarspalten aus Online-Medienangeboten oder gesammelte Hasskommentare von Anti-Diskriminierungsstellen zu verwenden. Diese waren aber meist zu wenig strukturiert oder schlicht nicht zugänglich. 

Schlussendlich griff ich auf drei Datensätze aus der Forschung zurück, die ich zu einem grossen Datensatz kombinierte. Die verwendeten Datensätze:

* Datensatz des Graduiertenkollegs «Nutzerzentrierte Soziale Medien» der Universität Duisburg-Essen: [Link](https://github.com/UCSM-DUE/IWG_hatespeech_public)
* Datensatz aus einer Arbeit von Uwe Bretschneider und Ralf Peters, Martin-Luther-Universität Halle-Wittenberg: [Link](http://www.ub-web.de/research/)
* Polly-Korpus von Tom De Smedt (Universität Antwerpen) und Sylvia Jaki (Universität Hildesheim): [Link](https://docs.google.com/spreadsheets/d/1c5peNMjt24U0FcEMSj8gD_JjzumqXTWbPWa_yb2nNt0/edit#gid=2031183870)

Diese Datensätze musste ich zuerst in die gleiche technische und inhaltliche Form bringen, da verschiedene Speicherformate und unterschiedliche Annotationsschemen verwendet wurden. [Im kombinierten Datensatz](https://github.com/pscllbssr/hatespeech-ml/blob/master/2_Feature_Engineering/export/combined_polly_bretschneider_iwg.xlsx) sind der Rohtext des Kommentars, die durchschnittliche Einschätzung aller Experten (Zahl zwischen 0 und 1), sowie davon abgeleitet eine binäre Einschätzung des Kommentars (Hasskommentar ja/nein) enthalten. Zudem habe ich Texte unter 50 Zeichen (schwer zu verstehen) und Texte über 300 Zeichen (maximale Tweet Länge: 280 Zeichen) herausgefiltert, um es der Maschine einfacher zu machen. 

Der Code dafür findet sich in den einzelnen Notebooks:
* [Kombination der Datensätze](https://github.com/pscllbssr/hatespeech-ml/blob/master/2_Feature_Engineering/8%20combine%20IWG%2C%20Bretschneider%20and%20polly%20subset.ipynb)
* Umwandlung der einzelnen Datensätze (Links)

## Feature-Engineering

Ein zentraler Punkt beim Machine Learning ist das sogenannte «Feature-Engineering», also das Auswählen und Aufbereiten verschiedener Merkmale eines Datensatzes. Als erstes musste ich die Texte zur weiteren Verarbeitung durch die Maschine vorbereiten. 

In einem ersten Schritt anonymisiere ich Benutzernamen, falls das nicht bereits in den Quell-Datensätzen geschehen ist. Zudem werden Elemente entfernt, die nichts direkt mit dem untersuchten Text zu tun haben, beispielsweise Links oder plattformspezifische Ausdrücke wie RT (Hinweis auf einen Retweet bei Twitter). Diese Elemente würden ansonsten das Resultat verzerren. 

In einem weiteren Schritt entferne ich «Stopwords» aus dem Text. Stopwords sind Wörter, die in einer Sprache oft vorkommen und nicht wirklich zur Aussage eines Textes beitragen. (Stopwords-Erklärung). Anschliessend benutzte ich einen [Lemmatizer](https://nlp.stanford.edu/IR-book/html/htmledition/stemming-and-lemmatization-1.html), um Wörter auf ihre Grundform zu reduzieren. Konjugierte Verben werden so als dasselbe Wort angesehen, also beispielsweise «bin», «bist» und «sind» werden in dieselbe Grundform «sein» umgewandelt. 

[Code der Umwandlung](https://github.com/pscllbssr/hatespeech-ml/blob/master/0_common/model_helpers.py)

Der wichtigste Schritt in der maschinellen Sprachverarbeitung ist das Vektorisieren, also die Umwandlung von Buchstaben in Zahlen. Dies ist nötig, weil Computer nicht mit Worten oder Buchstaben rechnen, sondern mit Zahlen. Dafür gibt es verschiedene Methoden. Während der Entwicklung meines Machine Learning Modells habe ich verschiedene Verfahren ausprobiert. Schlussendlich habe ich mit einem simplen [CountVectorizer](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.CountVectorizer.html) auf der Basis von Wortteilen die besten Resultate erzielt. Diese Erfahrung findet sich auch in der Wissenschaft wieder, beispielsweise bei Schmidt und Wiegand (2017, [Link](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.CountVectorizer.html)). Wortteile, anstelle von ganzen Wörter, bringen eine gewisse Toleranz gegenüber Rechtschreibefehlern mit sich, was bei Hasskommentaren nicht unrelevant ist.



“Fairly generic features, such as bag of words or embeddings, systematically yield reasonable classification performance. Character-level approaches work better than token-level approaches» https://www.aclweb.org/anthology/W17-1101

«Mehdad and Tetreault (2016) systematically compare character n-gram features with token n-grams for hate speech detection, and find that character n-grams prove to be more predictive than token n-grams»  https://www.aclweb.org/anthology/W17-1101

«Hate speech and sentiment analysis are closely related, and it is safe to assume that usually negative sentiment pertains to a hate speech message» https://www.aclweb.org/anthology/W17-1101
konnte ich in meinem Beispiel, mit einem Korpus getestet, nicht bestätigen

## Auswahl der Algorithmen und Modell-Training

finales Modell: Link

API und Modell Open Source - gerne weiter verwenden