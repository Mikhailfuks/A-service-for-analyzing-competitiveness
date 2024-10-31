using System;
using System.Collections.Generic;
using System.Linq;
using System.Text.RegularExpressions;

namespace CodeCompetitivenessAnalyzer
{
    public class CompetitivenessAnalyzer
    {
        private const string RegexPattern = @"^\s*(?<keyword>public|private|protected|static|abstract|virtual|sealed|override)\s+(?<type>[a-zA-Z0-9\.]+)\s+(?<name>[a-zA-Z0-9_]+)\s*\(";

        public CompetitivenessAnalysis Analyze(string code)
        {
            var analysis = new CompetitivenessAnalysis();
            var matches = Regex.Matches(code, RegexPattern);

            foreach (Match match in matches)
            {
                var keyword = match.Groups["keyword"].Value;
                var type = match.Groups["type"].Value;
                var name = match.Groups["name"].Value;

                // Analyze keywords
                if (keyword == "public")
                {
                    analysis.PublicMembers++;
                }
                else if (keyword == "private")
                {
                    analysis.PrivateMembers++;
                }
                else if (keyword == "protected")
                {
                    analysis.ProtectedMembers++;
                }
                else if (keyword == "static")
                {
                    analysis.StaticMembers++;
                }
                else if (keyword == "abstract")
                {
                    analysis.AbstractMembers++;
                }
                else if (keyword == "virtual")
                {
                    analysis.VirtualMembers++;
                }
                else if (keyword == "sealed")
                {
                    analysis.SealedMembers++;
                }
                else if (keyword == "override")
                {
                    analysis.OverrideMembers++;
                }

                // Analyze member types
                if (type.Contains("class"))
                {
                    analysis.Classes++;
                }
                else if (type.Contains("interface"))
                {
                    analysis.Interfaces++;
                }
                else if (type.Contains("enum"))
                {
                    analysis.Enums++;
                }
                else if (type.Contains("struct"))
                {
                    analysis.Structs++;
                }
                else if (type.Contains("delegate"))
                {
                    analysis.Delegates++;
                }

                // Analyze member names
                if (name.StartsWith("I"))
                {
                    analysis.InterfaceNames++;
                }
            }

            // Calculate competitiveness score
            analysis.Score = CalculateCompetitivenessScore(analysis);

            return analysis;
        }

        private double CalculateCompetitivenessScore(CompetitivenessAnalysis analysis)
        {
            // Adjust the weights and thresholds based on your specific needs
            double score = 0;

            score += analysis.PublicMembers * 0.5;
            score += analysis.StaticMembers * 0.2;
            score += analysis.AbstractMembers * 0.3;
            score += analysis.VirtualMembers * 0.4;
            score += analysis.Interfaces * 0.7;
            score += analysis.InterfaceNames * 0.6;

            if (analysis.Classes > 10)
            {
                score -= 0.2 * (analysis.Classes - 10);
            }

            if (analysis.PrivateMembers > 50)
            {
                score -= 0.1 * (analysis.PrivateMembers - 50);
            }

            return score;
        }
    }

    public class CompetitivenessAnalysis
    {
        public int PublicMembers { get; set; }
        public int PrivateMembers { get; set; }
        public int ProtectedMembers { get; set; }
        public int StaticMembers { get; set; }
        public int AbstractMembers { get; set; }
        public int VirtualMembers { get; set; }
        public int SealedMembers { get; set; }
        public int OverrideMembers { get; set; }

        public int Classes { get; set; }
        public int Interfaces { get; set; }
        public int Enums { get; set; }
        public int Structs { get; set; }
        public int Delegates { get; set; }

        public int InterfaceNames { get; set; }

        public double Score { get; set; }
    }

    public class Program
    {
        public static void Main(string[] args)
        {
            var analyzer = new CompetitivenessAnalyzer();
            var code = @"
                public class MyClass
                {
                    private int MyPrivateField;

                    public void MyPublicMethod()
                    {
                        // Code logic
                    }

                    public static void MyStaticMethod()
                    {
                        // Code logic
                    }
                }

                public interface IMyInterface
                {
                    void MyInterfaceMethod();
                }
            ";

            var analysis = analyzer.Analyze(code);

            Console.WriteLine($"Competitiveness Score: {analysis.Score}");
            Console.WriteLine($"Public Members: {analysis.PublicMembers}");
            Console.WriteLine($"Private Members: {analysis.PrivateMembers}");
            Console.WriteLine($"Static Members: {analysis.StaticMembers}");
            Console.WriteLine($"Interfaces: {analysis.Interfaces}");
            Console.WriteLine($"Interface Names: {analysis.InterfaceNames}");

            Console.ReadKey();
        }
    }
}
