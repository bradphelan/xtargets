--- 
title: Generate c++ backtrace under linux
date: 11/08/2010
layout: post
--- 


Just instantiate the below class and feed it to some iostream
compatible instance such as std::cout or std::cerr or std::stringstream


BackTrace.h

    /** @namespace */

    #include <iostream>

    struct BackTrace {

      void print(std::ostream & os) const;

      friend std::ostream & operator << ( std::ostream & os, const BackTrace & b){
        b.print(os);
        return os;
      }

    };



BackTrace.cpp

    #include "BackTrace.h"

    #include <stdio.h>
    #include <stdlib.h>
    #include <string.h>

    #if defined(__GNUC__)
    #if defined(__GLIBC__)
    #ifndef __UCLIBC__
    #define _HAVE_EXECINFO_H_ 1
    #endif
    #endif
    #endif

    #ifdef _HAVE_EXECINFO_H_
    #include <execinfo.h>
    #include <cxxabi.h>

    namespace {
        void printtrace(char ** symbol, size_t depth, std::ostream & os) {
            for (size_t i = 1; i < depth; i++) {
                char *begin = 0, *end = 0;
                // find the parentheses and address offset surrounding the mangled name
                for (char *j = symbol[i]; *j; ++j) {
                    if (*j == '(') {
                        begin = j;
                    } else if (*j == '+') {
                        end = j;
                    }
                }
                if (begin && end) {
                    *begin++ = ' ';
                    *end = 0;
                    // found our mangled name, now in [begin, end)

                    int status;
                    size_t sz;
                    char *ret = abi::__cxa_demangle(begin, 0, &sz, &status);
                    if (ret) {
                        // return value may be a realloc() of the input
                        os << ret << std::endl;
                        free(ret);
                    } else {
                        // demangling failed, just pretend it's a C function with no args
                        os << symbol[i] << std::endl;
                    }

                } else {
                    os << symbol[i];
                }
            }
        }
    }
    #endif

    void BackTrace::print(std::ostream & os) const {

    #define SIZE 100
    #if _HAVE_EXECINFO_H_
        void *array[SIZE];
        size_t size;
        char **symbols;

        size = backtrace(array, SIZE);
        symbols = backtrace_symbols(array, size);

        os << std::endl << "Obtained " << size << " stack frames." << std::endl;

        printtrace(symbols, size, os);
        free(symbols);
    #endif
    }



