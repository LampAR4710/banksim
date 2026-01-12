[bankacc.ipynb](https://github.com/user-attachments/files/24559362/bankacc.ipynb)
{
 "cells": [
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "a05155df",
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "생성된 계좌 개수: 3\n",
      "\n",
      "[잔고 100만원 이상 고객]\n",
      "은행이름: SC은행, 예금주: 파이썬, 계좌번호: 447-65-983372, 잔고: 1,000,000원\n",
      "은행이름: SC은행, 예금주: C언어, 계좌번호: 372-61-469275, 잔고: 1,200,000원\n",
      "\n",
      "[파이썬] 입금 내역\n",
      "입금 내역이 없습니다.\n",
      "\n",
      "[파이썬] 출금 내역\n",
      "출금 내역이 없습니다.\n"
     ]
    }
   ],
   "source": [
    "import random\n",
    "\n",
    "\n",
    "class Account:\n",
    "    # Q2) 클래스 변수: 전체 계좌 개수 저장\n",
    "    account_count = 0\n",
    "    # 은행이름은 고정 (모든 계좌 공통)\n",
    "    bank_name = \"SC은행\"\n",
    "    # (선택) 계좌번호 중복 방지용 세트\n",
    "    _used_account_numbers = set()\n",
    "    def __init__(self, owner, balance):\n",
    "        \"\"\"\n",
    "        Q1) 생성자: 예금주(owner), 초기잔액(balance)만 입력 받기\n",
    "        \"\"\"\n",
    "        if balance < 0:\n",
    "            raise ValueError(\"초기 잔액은 0원 이상이어야 합니다.\")\n",
    "        self.owner = owner\n",
    "        self.balance = balance\n",
    "        # 입금 횟수(이자 지급 조건용)\n",
    "        self.deposit_count = 0\n",
    "        # Q10) 입금/출금 내역 기록\n",
    "        self._deposit_history = []   # 입금 내역 리스트\n",
    "        self._withdraw_history = []  # 출금 내역 리스트\n",
    "        # Q1) 계좌개설 메서드로 은행이름/계좌번호 세팅\n",
    "        self.open_account()\n",
    "        # Q2) 계좌 객체 수 증가\n",
    "        Account.account_count += 1\n",
    "\n",
    "    def open_account(self):\n",
    "        \"\"\"\n",
    "        Q1) 계좌개설 메서드:\n",
    "        - 은행이름은 SC은행\n",
    "        - 계좌번호는 3자리-2자리-6자리 랜덤 생성\n",
    "        \"\"\"\n",
    "        self.bank = Account.bank_name\n",
    "        self.account_number = self._generate_account_number()\n",
    "\n",
    "    @classmethod\n",
    "    def _generate_account_number(cls):\n",
    "        \"\"\"\n",
    "        3자리-2자리-6자리 형태의 랜덤 계좌번호 생성\n",
    "        예) 111-11-111111\n",
    "        \"\"\"\n",
    "        while True:\n",
    "            part1 = f\"{random.randint(0, 999):03d}\"\n",
    "            part2 = f\"{random.randint(0, 99):02d}\"\n",
    "            part3 = f\"{random.randint(0, 999999):06d}\"\n",
    "            number = f\"{part1}-{part2}-{part3}\"\n",
    "\n",
    "            # (선택) 중복 방지\n",
    "            if number not in cls._used_account_numbers:\n",
    "                cls._used_account_numbers.add(number)\n",
    "                return number\n",
    "\n",
    "    # Q3) 계좌 개수 출력 메서드\n",
    "    @classmethod\n",
    "    def get_account_num(cls):\n",
    "        print(f\"생성된 계좌 개수: {cls.account_count}\")\n",
    "        return cls.account_count\n",
    "\n",
    "    # Q4) 입금 메서드\n",
    "    def deposit(self, amount):\n",
    "        \"\"\"\n",
    "        - 입금은 최소 1원 이상만 가능\n",
    "        - Q7) 입금 횟수가 5회가 될 때(5,10,15...) 잔고의 1% 이자 지급\n",
    "        - Q10) 입금 내역 기록\n",
    "        \"\"\"\n",
    "        if amount < 1:\n",
    "            print(\"입금은 최소 1원 이상만 가능합니다.\")\n",
    "            return False\n",
    "\n",
    "        self.balance += amount\n",
    "        self.deposit_count += 1\n",
    "\n",
    "        # 입금 내역 기록\n",
    "        self._deposit_history.append({\n",
    "            \"type\": \"입금\",\n",
    "            \"amount\": amount,\n",
    "            \"balance\": self.balance\n",
    "        })\n",
    "\n",
    "        # Q7) 5회마다 이자 지급(잔고 기준 1%)\n",
    "        if self.deposit_count % 5 == 0:\n",
    "            interest = int(self.balance * 0.01)  # 돈이니 정수 처리\n",
    "            self.balance += interest\n",
    "\n",
    "            # 이자도 내역에 기록(입금 내역으로 처리)\n",
    "            self._deposit_history.append({\n",
    "                \"type\": \"이자\",\n",
    "                \"amount\": interest,\n",
    "                \"balance\": self.balance\n",
    "            })\n",
    "\n",
    "        return True\n",
    "\n",
    "    # Q5) 출금 메서드\n",
    "    def withdraw(self, amount):\n",
    "        \"\"\"\n",
    "        - 잔고 이상 출금 불가\n",
    "        - Q10) 출금 내역 기록\n",
    "        \"\"\"\n",
    "        if amount < 1:\n",
    "            print(\"출금은 최소 1원 이상만 가능합니다.\")\n",
    "            return False\n",
    "\n",
    "        if amount > self.balance:\n",
    "            print(\"잔고가 부족하여 출금할 수 없습니다.\")\n",
    "            return False\n",
    "\n",
    "        self.balance -= amount\n",
    "\n",
    "        # 출금 내역 기록\n",
    "        self._withdraw_history.append({\n",
    "            \"type\": \"출금\",\n",
    "            \"amount\": amount,\n",
    "            \"balance\": self.balance\n",
    "        })\n",
    "\n",
    "        return True\n",
    "\n",
    "    # Q6) 정보 출력 메서드\n",
    "    def display_info(self):\n",
    "        \"\"\"\n",
    "        잔고는 세자리마다 쉼표 출력:\n",
    "        f\"{숫자:,}\" 형태 사용\n",
    "        \"\"\"\n",
    "        print(\n",
    "            f\"은행이름: {self.bank}, 예금주: {self.owner}, \"\n",
    "            f\"계좌번호: {self.account_number}, 잔고: {self.balance:,}원\"\n",
    "        )\n",
    "\n",
    "    # Q10) 입금 내역 출력 메서드\n",
    "    def deposit_history(self):\n",
    "        print(f\"\\n[{self.owner}] 입금 내역\")\n",
    "        if not self._deposit_history:\n",
    "            print(\"입금 내역이 없습니다.\")\n",
    "            return\n",
    "\n",
    "        for i, item in enumerate(self._deposit_history, start=1):\n",
    "            print(f\"{i}. {item['type']}: {item['amount']:,}원 -> 잔고 {item['balance']:,}원\")\n",
    "\n",
    "    # Q10) 출금 내역 출력 메서드\n",
    "    def withdrawal_history(self):\n",
    "        print(f\"\\n[{self.owner}] 출금 내역\")\n",
    "        if not self._withdraw_history:\n",
    "            print(\"출금 내역이 없습니다.\")\n",
    "            return\n",
    "\n",
    "        for i, item in enumerate(self._withdraw_history, start=1):\n",
    "            print(f\"{i}. {item['type']}: {item['amount']:,}원 -> 잔고 {item['balance']:,}원\")\n",
    "\n",
    "\n",
    "# -----------------------\n",
    "# 아래는 사용 예시 (Q8, Q9 포함)\n",
    "# -----------------------\n",
    "if __name__ == \"__main__\":\n",
    "    # Q8) 3개 이상 인스턴스 생성 후 리스트에 저장\n",
    "    accounts = [\n",
    "        Account(\"파이썬\", 1_000_000),\n",
    "        Account(\"자바\", 500_000),\n",
    "        Account(\"C언어\", 1_200_000),\n",
    "    ]\n",
    "\n",
    "    # Q3) 생성된 계좌 수 출력\n",
    "    Account.get_account_num()\n",
    "\n",
    "    \n",
    "\n",
    "    # Q9) 잔고가 100만원 이상인 고객 정보만 출력\n",
    "    print(\"\\n[잔고 100만원 이상 고객]\")\n",
    "    for acc in accounts:\n",
    "        if acc.balance >= 1_000_000:\n",
    "            acc.display_info()\n",
    "\n",
    "    # Q10) 입금/출금 내역 출력\n",
    "    accounts[0].deposit_history()\n",
    "    accounts[0].withdrawal_history()\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 112,
   "id": "9aa14130",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "<__main__.Account at 0x103c97a80>"
      ]
     },
     "execution_count": 112,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "Account(owner,balance)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 115,
   "id": "7800afd1",
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "생성된 계좌 개수: 4\n",
      "은행이름: SC은행, 예금주: 파이썬, 계좌번호: 447-65-983372, 잔고: 1,000,000원\n"
     ]
    }
   ],
   "source": [
    "accounts[0].get_account_num()\n",
    "accounts[0].display_info()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 116,
   "id": "bb2df7cf",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "True"
      ]
     },
     "execution_count": 116,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "amount = int(input(\"얼마 입금할까요? \"))\n",
    "accounts[0].deposit(amount)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 96,
   "id": "b39c72d3",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "True"
      ]
     },
     "execution_count": 96,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "amount = int(input(\"얼마 출금할까요? \"))\n",
    "accounts[0].withdraw(amount)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "76788d99",
   "metadata": {},
   "outputs": [],
   "source": [
    "# 몇 번 입금/출금 해보기 (이자 확인용)\n",
    "accounts[0].deposit(10_000)\n",
    "accounts[0].deposit(10_000)\n",
    "accounts[0].deposit(10_000)\n",
    "accounts[0].deposit(10_000)\n",
    "accounts[0].deposit(10_000)  \n",
    "# 5번째 입금 -> 이자(1%) 지급 발생\n",
    "accounts[0].deposit_history()\n",
    "\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 120,
   "id": "ccb8e018",
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "잔고가 부족하여 출금할 수 없습니다.\n"
     ]
    },
    {
     "data": {
      "text/plain": [
       "False"
      ]
     },
     "execution_count": 120,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "#잔고 부족 테스트    \n",
    "accounts[1].withdraw(600_000_000_000_000_000_000_000_000_000_000) "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 117,
   "id": "07c6f9b5",
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "은행이름: SC은행, 예금주: 파이썬, 계좌번호: 447-65-983372, 잔고: 1,000,999,999원\n",
      "\n",
      "[파이썬] 입금 내역\n",
      "1. 입금: 999,999,999원 -> 잔고 1,000,999,999원\n",
      "\n",
      "[파이썬] 출금 내역\n",
      "출금 내역이 없습니다.\n"
     ]
    }
   ],
   "source": [
    "accounts\n",
    "accounts[0].display_info()\n",
    "accounts[0].deposit_history()\n",
    "accounts[0].withdrawal_history()\n"
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "base",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.13.9"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 5
}
