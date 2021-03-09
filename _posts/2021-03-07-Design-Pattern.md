---
layout: post
title: Design Pattern
date: 2021-03-07
categories: [technology]
tags: [coding]
comments: false
---



开个新坑，关于设计模式。分为：**创建（creational）**、**结构（structure）**和**行为（behavior）**三大类。



### creation pattern

- abstract factory
- factory method
- singleton
- Builder 
- prototype



### structure pattern



### behavior pattern



### Example

- [Design flight ticket](https://www.1point3acres.com/bbs/thread-686689-1-1.html)

My design:

```c++
// main.cpp
#include <iostream>
#include <vector>
#include <sstream>
#include <string>

#include "flight_ticket.h"

namespace zoox {

namespace {

constexpr int kTicketSize = 3;

}  // namespace

std::vector<std::string> ParseTicketString(const std::string& ticket) {
  std::vector<std::string> ticket_str;
  std::istringstream str_input(ticket);
  std::string cur_str;
  while (std::getline(str_input, cur_str, ' ')) {
    ticket_str.emplace_back(cur_str);
  }
  return ticket_str;
}

void RunMain() {
  PrintTest();

  std::vector<std::string> input_tickets
    {"United 150.0 Premium", "United 120.0 Economy",
     "United 100.0 Business", "Delta 60.0 Economy",
     "Delta 60.0 Premium", "Delta 60.0 Business", 
     "SouthWest 1000.0 Economy", "SouthWest 4000.0 Economy",
     "SouthWest 4000.0 Economy hhhhhhhh", "LuigiAir 50.0 Business"};   

  std::string::size_type sz; 
  for (const auto& ticket : input_tickets) {
    const std::vector<std::string> ticket_strs = ParseTicketString(ticket); 
    if (ticket_strs.size() != kTicketSize) {
      std::cout << "Illegal ticket " << ticket << std::endl;
    } else {
      const FlightTicket flight_ticket(std::stod(ticket_strs[1]),
           ticket_strs[2], ticket_strs[0]);
      const double price = 
        FlightTicketSystem::Instance(flight_ticket.airline)
         ->GetTicketPrice(flight_ticket);
      std::cout << "Ticket " << ticket << " 's cost " << price << std::endl;     
    }
  }
}

} // namespace zoox

int main() {
  zoox::RunMain();

  return 0;
}

// flight_ticket.h
#ifndef FLIGHT_TICKTET_H_
#define FLIGHT_TICKTET_H_

#include <iostream>
#include <unordered_map>

namespace zoox {

inline void PrintTest() {
  std::cout << "Test hello world!!!\n";
}

enum class SeatLevel {
  Economy, Premium, Business
};

enum class Airline {
  Delta, United, SouthWest, LuigiAir
};

const std::unordered_map<std::string, SeatLevel> seat_level_map_{
  {"Economy", SeatLevel::Economy},
  {"Premium", SeatLevel::Premium},
  {"Business", SeatLevel::Business}
};

const std::unordered_map<std::string, Airline> airline_map_{
  {"Delta", Airline::Delta},
  {"United", Airline::United},
  {"SouthWest", Airline::SouthWest},
  {"LuigiAir", Airline::LuigiAir}
};

struct FlightTicket {
  double distance;
  SeatLevel seat_level;
  Airline airline;
  FlightTicket() = delete;
  FlightTicket(const double dist, const std::string level_str, 
               const std::string& airline_str) {
    distance = dist;
    seat_level = seat_level_map_.at(level_str);
    airline = airline_map_.at(airline_str);
  }
};

class FlightTicketSystem {
 public:
  static FlightTicketSystem* Instance(const Airline airline);

  double GetOperationCost(const FlightTicket& ticket) const {
    if (ticket.seat_level == SeatLevel::Economy) {
      return 0.0;
    } else if (ticket.seat_level == SeatLevel::Premium) {
      return 25.0;
    } else if (ticket.seat_level == SeatLevel::Business) {
      return 50.0 + ticket.distance*0.25;
    }
    return 0.0;
  }

  virtual double GetTicketPrice(const FlightTicket& ticket) const = 0;
};

class Delta : public FlightTicketSystem {
 public:
  static FlightTicketSystem* Instance() {
    if (!instance_) {
      instance_ = new Delta();
    }
    return instance_; 
  } 

  virtual double GetTicketPrice(const FlightTicket& ticket) const override {
    return GetOperationCost(ticket) + 0.5 * ticket.distance;
  }

  static Delta *instance_;
};

class United : public FlightTicketSystem {
 public:
  static United* Instance() {
    if (!instance_) {
      instance_ = new United();
    }
    return instance_; 
  } 

  double GetTicketPrice(const FlightTicket& ticket) const override {
    return GetOperationCost(ticket) + 0.75 * ticket.distance + 0.1 * ticket.distance
        * (ticket.seat_level == SeatLevel::Premium);
  }

  static United *instance_;
};

class SouthWest : public FlightTicketSystem {
 public:
  static SouthWest* Instance() {
    if (!instance_) {
      instance_ = new SouthWest();
    }
    return instance_; 
  } 
  
  double GetTicketPrice(const FlightTicket& ticket) const override {
    return 1.0 * ticket.distance;
  }

  static SouthWest *instance_;
};

class LuigiAir : public FlightTicketSystem {
 public:
  static LuigiAir* Instance() {
    if (!instance_) {
      instance_ = new LuigiAir();
    }
    return instance_; 
  } 

  double GetTicketPrice(const FlightTicket& ticket) const override {
    return std::max(100.0, GetOperationCost(ticket)*2.0);
  }

  static LuigiAir *instance_;
};

FlightTicketSystem* FlightTicketSystem::Instance(const Airline airline) {
    if (airline == Airline::Delta) {
      return Delta::Instance();
    } else if (airline == Airline::United) {
      return United::Instance();
    } else if (airline == Airline::SouthWest) {
      return SouthWest::Instance();
    } else if (airline == Airline::LuigiAir) {
      return LuigiAir::Instance();
    }
    return nullptr;
}

Delta* Delta::instance_ = nullptr;
United* United::instance_ = nullptr;
SouthWest* SouthWest::instance_ = nullptr;
LuigiAir* LuigiAir::instance_ = nullptr;

} // namespace zoox

#endif
```

Ref:  

https://repl.it/@DavidC25/flightticket#main.cpp

https://repl.it/@soulmachine/AirlineCalculator 



**Reference:**

Book <Pattern Design>

[Website](https://refactoring.guru/design-patterns) 