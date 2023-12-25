# 3sem_lab8
## Homework
### Задание 1
Используя диаграмму классов из лабораторной работы №7, реализуйте поддержку сериализации и десериализации класса Animal.

Создайте экземпляр класса Animal, инициализируйте все поля и выполните его Xml-сериализацию.
	Добавьте в решение новый консольный проект, в котором десериализуйте класс Animal и выведите полученный объект на консоль.
```
using System.Xml.Serialization;
using AnimalLibrary;

class AnimalConsoleApp
{
    static void Main(string[] args)
    {
        Animal animal = new Cow("USA", "Bessie", "A friendly cow", false);
        //экземпляр создается с использованием конструктора Cow, который принимает аргументы USA, Bessie, A friendly cow и false.
        //Создается экземпляр класса XmlSerializer, который будет использоваться для сериализации и десериализации объектов типа Animal
        XmlSerializer serializer = new XmlSerializer(typeof(Animal));
        //для создания экземпляра класса StreamWriter, который будет использоваться для записи сериализованного объекта в файл с именем "animal.xml"
        using (TextWriter writer = new StreamWriter("animal.xml"))
        {//Вызывается метод Serialize объекта serializer, чтобы сериализовать объект animal и записать его в файл с использованием объекта writer
            serializer.Serialize(writer, animal);
        }

        Console.WriteLine("Animal object has been serialized to animal.xml");

        using (TextReader reader = new StreamReader("animal.xml"))
        {//для создания экземпляра класса StreamReader, который будет использоваться для чтения сериализованного объекта из файла "animal.xml"
            Animal deserializedAnimal = (Animal)serializer.Deserialize(reader);
            //Вызывается метод Deserialize объекта serializer для десериализации объекта из файла с использованием объекта reader
            Console.WriteLine("\nDeserialized Animal Object:");
            Console.WriteLine($"Name: {deserializedAnimal.Name}");
            Console.WriteLine($"Country: {deserializedAnimal.Country}");
            Console.WriteLine($"Description: {deserializedAnimal.Description}");
            Console.WriteLine($"HideFromOtherAnimals: {deserializedAnimal.HideFromOtherAnimals}");
            //Вызывается метод SayHello() на десериализованном объекте deserializedAnimal, чтобы он вывел приветствие в консоль
            deserializedAnimal.SayHello();
            Console.WriteLine($"Favorite Food: {deserializedAnimal.GetFavouriteFood()}");
        }
    }
}

```

AnimalLibrary;
```
using System.Xml.Serialization;

namespace AnimalLibrary
{
    [AttributeUsage(AttributeTargets.Class)]
    public class CommentAtt : Attribute
    {
        public string Comment { get; set; }

        public CommentAtt(string comment)
        {
            Comment = comment;
        }
    }

    public enum AnimalClassification
    {
        Herbivores,
        Carnivores,
        Omnivores
    }

    public enum FavouriteFood
    {
        Meat,
        Plants,
        Everything
    }

    [Serializable]
    [XmlInclude(typeof(Cow))]
    [XmlInclude(typeof(Lion))]
    [XmlInclude(typeof(Pig))]
    [XmlRoot("Animal")]

    [CommentAtt("Абстрактный класс для объектов, представляющих животных")]
    public abstract class Animal
    {
        private AnimalClassification classification;

        [XmlElement("Name")]
        public string Name { get; set; }

        [XmlElement("Country")]
        public string Country { get; set; }

        [XmlElement("Description")]
        public string Description { get; set; }

        [XmlElement("HideFromOtherAnimals")]
        public bool HideFromOtherAnimals { get; set; }

        public abstract void SayHello();
        public abstract FavouriteFood GetFavouriteFood();

        public Animal(string country, string name, string description, bool hideFromOtherAnimals)
        {
            Name = name;
            Country = country;
            Description = description;
            HideFromOtherAnimals = hideFromOtherAnimals;
        }

        public void Deconstruct(out string out_name)
        {
            out_name = Name;
        }

        public void Deconstruct(out string out_name, out string out_desc)
        {
            out_name = Name;
            out_desc = Description;
        }

        public void Deconstruct(out string out_name, out string out_desc, out string out_count)
        {
            out_name = Name;
            out_count = Country;
            out_desc = Description;
        }

        public void Deconstruct(out string out_name, out string out_desc, out string out_count, out bool out_hide)
        {
            out_name = Name;
            out_count = Country;
            out_desc = Description;
            out_hide = HideFromOtherAnimals;
        }
    }

    [CommentAtt("Класс описания коровы")]
    public class Cow : Animal
    {
        public Cow() : base("", "", "", false)
        {
        }

        public Cow(string country, string name, string description, bool hideFromOtherAnimals) :
            base(country, name, description, hideFromOtherAnimals)
        {
        }

        public override FavouriteFood GetFavouriteFood()
        {
            return FavouriteFood.Plants;
        }

        public override void SayHello()
        {
            Console.WriteLine("Moo\n");
        }
    }

    [CommentAtt("Класс описания льва")]
    public class Lion : Animal
    {
        public Lion() : base("", "", "", false)
        {
        }

        public Lion(string country, string name, string description, bool hideFromOtherAnimals) :
            base(country, name, description, hideFromOtherAnimals)
        {
        }

        public override FavouriteFood GetFavouriteFood()
        {
            return FavouriteFood.Meat;
        }

        public override void SayHello()
        {
            Console.WriteLine("Roar\n");
        }
    }

    [CommentAtt("Класс описания свиньи")]
    public class Pig : Animal
    {
        public Pig() : base("", "", "", false)
        {
        }

        public Pig(string country, string name, string description, bool hideFromOtherAnimals) :
            base(country, name, description, hideFromOtherAnimals)
        {
        }

        public override FavouriteFood GetFavouriteFood()
        {
            return FavouriteFood.Everything;
        }

        public override void SayHello()
        {
            Console.WriteLine("Oink\n");
        }
    }
}
```


### Задание 2
Напишите приложение для поиска заданного файла во всех поддиректориях указанного пользователем пути.
Используйте FileStream для вывода содержимого файла на консоль.
Добавьте возможность сжатия найденного файла стандартными средствами .Net.

```
using System;
using System.IO.Compression;

class lab082
{
    static void Main(string[] args)
    {
        Console.WriteLine("Введите путь к папке, в которой нужно выполнить поиск:");
        string searchDirectory = Console.ReadLine();

        Console.WriteLine("Введите имя файла для поиска (с расширением, например, example.txt):");
        string targetFileName = Console.ReadLine();
        //использует метод GetFiles из класса Directory для поиска файлов в указанной папке(searchDirectory)
        //Он использует значение targetFileName в качестве шаблона имени файла и SearchOption.AllDirectories для поиска 
        //во всех подпапках. Результаты поиска сохраняются в массиве строк files
        string[] files = Directory.GetFiles(searchDirectory, targetFileName, SearchOption.AllDirectories);

        if (files.Length == 0)
        {
            Console.WriteLine("Файл не найден.");
        }
        else
        {
            foreach (string filePath in files)
            {
                Console.WriteLine($"Найден файл: {filePath}");
                //Этот код открывает файл на чтение, используя класс `File` и метод `OpenRead()`, и создает объект `FileStream`,
                //который представляет поток для чтения файла
                using (FileStream fileStream = File.OpenRead(filePath))
                {//Этот код создает объект `StreamReader`, который обеспечивает чтение текста из объекта `FileStream` (`fileStream`)
                    using (StreamReader reader = new StreamReader(fileStream))
                    {//Этот код использует метод `ReadToEnd()` объекта `StreamReader` для чтения содержимого файла и присваивает
                     //его значение переменной `fileContent`
                        string fileContent = reader.ReadToEnd();
                        Console.WriteLine($"Содержимое файла:\n{fileContent}");
                    }
                }
                //тот код использует метод `ChangeExtension()` из класса `Path` для изменения расширения файла (`filePath`)
                //на ".gz" и сохраняет новый путь к файлу в переменной `compressedFilePath`
                string compressedFilePath = Path.ChangeExtension(filePath, ".gz");
                //открывает исходный файл на чтение, используя метод `OpenRead()` из класса `File`, и создает объект `FileStream` (`originalFileStream`)
                using (FileStream originalFileStream = File.OpenRead(filePath))
                {//создает файл для записи, используя метод `Create()` из класса `File`, и создает объект `FileStream`
                 //(`compressedFileStream`), который представляет поток для записи сжатого файла
                    using (FileStream compressedFileStream = File.Create(compressedFilePath))
                    {//создает объект `GZipStream`, который обеспечивает сжатие данных при их записи в объект `compressedFileStream`.
                     //Он использует режим сжатия `CompressionMode.Compress`
                        using (GZipStream compressionStream = new GZipStream(compressedFileStream, CompressionMode.Compress))
                        {
                            originalFileStream.CopyTo(compressionStream);
                            Console.WriteLine($"Файл сжат и сохранен как: {compressedFilePath}");
                        }
                    }
                }
            }
        }
    }
}
```
