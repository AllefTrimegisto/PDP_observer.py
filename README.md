# PDP_observer.py

class Subject:
    def __init__(self):
        self._observers = []

    def registerObserver(self, observer):
        self._observers.append(observer)

    def removeObserver(self, observer):
        self._observers.remove(observer)

    def notifyObservers(self):
        for observer in self._observers:
            observer.update()

class TextEditor(Subject):
    def __init__(self):
        super().__init__()
        self._lines = []

    def insertLine(self, lineNumber, text):
        self._lines.insert(lineNumber, text)
        self.notifyObservers()

    def removeLine(self, lineNumber):
        del self._lines[lineNumber]
        self.notifyObservers()

    def getLines(self):
        return self._lines

class FileObserver:
    def __init__(self, textEditor, filename):
        self._textEditor = textEditor
        self._filename = filename

    def update(self):
        with open(self._filename, 'w') as f:
            for line in self._textEditor.getLines():
                f.write(line + '\n')

class PrintObserver:
    def __init__(self, textEditor):
        self._textEditor = textEditor

    def update(self):
        print('\n'.join(self._textEditor.getLines()))

def main():
    textEditor = TextEditor()

    fileObserver = FileObserver(textEditor, 'file.txt')
    textEditor.registerObserver(fileObserver)

    printObserver = PrintObserver(textEditor)
    textEditor.registerObserver(printObserver)

    textEditor.notifyObservers() # Evento "open"

    lineNumber = 0
    while True:
        line = input("Digite uma linha (EOF para sair): ")
        if line == 'EOF':
            break
        textEditor.insertLine(lineNumber, line)
        lineNumber += 1

    textEditor.notifyObservers() # Evento "save"

if __name__ == '__main__':
    main()
