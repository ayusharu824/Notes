
reverse a string in c# without using in built function

public static string ReverseString(string inputString){
        Console.WriteLine("printing");
        char[] chars = inputString.ToCharArray();
        int left = 0, right = chars.Length-1;
        char temp;
        Console.WriteLine(right);
        
        while(left<right){
            temp = chars[right];
            chars[right] = chars[left];
            chars[left] = temp;
            left++;
            right--;
        }
        return new string(chars);
    }

Second highest number in array
public static int SecondHighestNumber(int[] arr){
        int first = int.MinValue;
        int second = int.MinValue;
        
        foreach(int n in arr){
            if(n > first){
                second = first;
                first = n;
            }
            else if(n> second && n!=first){
                second = n;
            }
        }
        return second;
    }

defining in array
int[] numArr = new int[] { 1, 2, 3, 4, 5 };

public static bool checkPalindrome(string text){
        char[] ch = text.ToCharArray();
        int l = 0, r=ch.Length-1;
        while(l<r){
            if(ch[l]!=ch[r])
                return false;
                
        l++;
        r--;
        }
        return true;
    }

Reverse words in a sentence

i/p
"I love CSharp"
o/p
"CSharp love I"

string ReverseWords(string sentence)
{
    var words = sentence.Split(' ');
    Array.Reverse(words);
    return string.Join(" ", words);
}