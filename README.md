# Incubyte-TDD-Assessment

#include <iostream>
#include <sstream>
#include <vector>
#include <stdexcept>
#include <string>
#include <algorithm>

using namespace std;

int add(string numbers) {
    // Helper function to process the numbers with a given delimiter
    auto process_numbers = [](const string& numbers_part, const string& delimiter) -> int {
        stringstream ss(numbers_part);
        string number;
        vector<int> negatives;
        int sum = 0;

        while (getline(ss, number, delimiter[0])) {
            if (!number.empty()) {
                int num = stoi(number);
                if (num < 0) {
                    negatives.push_back(num);
                } else {
                    sum += num;
                }
            }
        }

        if (!negatives.empty()) {
            stringstream error_message;
            error_message << "negative numbers not allowed ";
            for (size_t i = 0; i < negatives.size(); ++i) {
                if (i > 0) {
                    error_message << ",";
                }
                error_message << negatives[i];
            }
            throw runtime_error(error_message.str());
        }

        return sum;
    };

    // Handle the case where the string is empty
    if (numbers.empty()) {
        return 0;
    }

    // If the string starts with "//", handle custom delimiters
    if (numbers[0] == '/' && numbers[1] == '/') {
        size_t delimiter_end = numbers.find('\n');
        string delimiter = numbers.substr(2, delimiter_end - 2);
        string numbers_part = numbers.substr(delimiter_end + 1);

        // Replace the custom delimiter with a single character delimiter for consistent processing
        string processed_numbers_part;
        for (char c : numbers_part) {
            if (c == delimiter[0] || c == '\n') {
                processed_numbers_part += ',';
            } else {
                processed_numbers_part += c;
            }
        }

        return process_numbers(processed_numbers_part, ",");
    }

    // Default handling for commas and new lines
    string processed_numbers;
    for (char c : numbers) {
        if (c == '\n') {
            processed_numbers += ',';
        } else {
            processed_numbers += c;
        }
    }

    return process_numbers(processed_numbers, ",");
}
int main() {
    try {
        cout << add("") << endl;            // Output: 0
        cout << add("1") << endl;           // Output: 1
        cout << add("1,5") << endl;         // Output: 6
        cout << add("1\n2,3") << endl;      // Output: 6
        cout << add("//;\n1;2") << endl;    // Output: 3
        cout << add("//|\n1|2|3") << endl; // Output: 6

        // This will throw an exception
        cout << add("//;\n1;-2;3;-4") << endl;
    } catch (const runtime_error& e) {
        cerr << e.what() << endl; // Output: negative numbers not allowed -2,-4
    }

    return 0;
}
