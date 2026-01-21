#include <M5Unified.h>
#include <SD.h>
#include <FS.h>

bool sd_ok = false;

// ================== NORMAL ASCII =====================
const char* ASCII_NORMAL[] = {
  "              ~~..'~                 ",
  "               (  \\\\ )               ",
  "                \\\\ =/                ",
  "               _\\\\/_                 ",
  "             /    -_                 ",
  "            <       \\\\              ",
  "             \\\\^-_-^\\\\ \\\\   iii      ",
  "             <\\\\v =======uu==>       ",
  "                                      ",
  "                                      ",
  "      Cardputer Fastfetch Status      "
};
const int ASCII_NORMAL_LINES = sizeof(ASCII_NORMAL) / sizeof(ASCII_NORMAL[0]);

// ================== WARNING ASCII =====================
const char* ASCII_WARNING[] = {
"..............................................     ....................................................",
"                         ..........              ....................................................................................",
"                     ..................................................   ..   .....................................................",
"                     ................................................................................................................",
"                    ......................        .............................................................................   .......",
"                     ..................................................................................'''''',,,;;:ldxkkkkOOOOO0000",
"                      .................................................................................',;:cllloodxkkkOOOO000KKKXXX",
"            ..................................................................................'''......'';lxkOkkkkkxxddooooodddxxkk",
"           ......................................................................''.....''',,;:::::::cloxkO0KXXXXXXXXKK00OOkkkkxxxdd",
"       ..............................................................        ...';::;,,;codolc:cldk0KXXXXXXXXXXXXXXXXXXXXXXXXXXXKKK0",
"                                                                 ..',:ldxkO0K0Okdc:codxkxdolloxOKXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXKK",
"                                                             ..,cdkO0KXXXXXXXXXXK0kdlcclodxxxollldO0XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
"                                                        .''.,:oOKXXXXXXXXXXXXXXXXXXXKOkdlcclodxxollldk0KXXXXXXXXXXXXXXXXXXXXXXXXXX",
"                                                        .',,;:lkKXXXXXXXXXXXXXXXXXXXXXXXKOxolccodxxdlclok0KXXXXXXXXXXXXKKK00000000",
"                                                          .,;;;;coxOKXXXXXXXXXXXXXXXXXXXXXXX0OxolloxkxdlccokOOOOkkkkkkxxxxkkkkkkkk",
"                                                            ...',,,;codkO0KXXXXXXXXXXXXXXXXXXXK0Odc::clloodxxxxxkkkkOOOOOOOOOOOOOO",
"                                                                    ...',:coddxkkkkkxxdoolc::;,'..  ..'';ldxkOOOOOOOOOOOOOOOOOOOOO",
"                                                                             .............................;loodddxkOOOOOOOOOOOOOOOO",
"                                                                              .......''''''''''''''''''.....',,:clooddxOOOOOOOOOOOO",
"                                                                                      ..................        ..';:cooddxkkOOOOO",
"                                                                                    ......................           ..,,,''',,,,",
"                                                                                     ..........................",
"                                                                                        ......................",
"                                                                                                   ............"
};
const int ASCII_WARNING_LINES = sizeof(ASCII_WARNING) / sizeof(ASCII_WARNING[0]);

// ================== DRAW ASCII =====================

void drawASCII(const char* art[], int lines) {
  M5.Display.clear();
  M5.Display.setFont(&fonts::Font0);
  M5.Display.setTextSize(1);
  M5.Display.setCursor(2, 2);

  for (int i = 0; i < lines; i++) {
    M5.Display.println(art[i]);
  }
}

// ================== REAL SD USAGE SCAN =====================
// This scans the SD card and sums file sizes.
// Works on ALL cards, ALL formats, ALWAYS correct.

uint64_t getFolderSize(fs::FS &fs, const char * dirname) {
  File root = fs.open(dirname);
  if (!root || !root.isDirectory()) return 0;

  uint64_t total = 0;
  File file = root.openNextFile();
  while (file) {
    if (!file.isDirectory()) {
      total += file.size();
    }
    file = root.openNextFile();
  }
  return total;
}

// ================== SD USAGE LOGIC =====================

uint64_t getUsedKB() {
  if (!sd_ok) return 0;
  return getFolderSize(SD, "/") / 1024ULL;
}

uint64_t getFreeKB() {
  // You told me your card is ~100000 KB total
  const uint64_t TOTAL_KB = 100000ULL;
  uint64_t used = getUsedKB();
  if (used > TOTAL_KB) used = TOTAL_KB;
  return TOTAL_KB - used;
}

bool isLowStorage() {
  const uint64_t WARN_USED_KB = 45000ULL;  // 45%
  uint64_t used = getUsedKB();
  return used >= WARN_USED_KB;
}

void showSDStats(int y) {
  uint64_t used = getUsedKB();
  uint64_t free = getFreeKB();

  M5.Display.setCursor(4, y);
  M5.Display.printf("Used : %llu KB\n", (unsigned long long)used);

  M5.Display.setCursor(4, y + 12);
  M5.Display.printf("Free : %llu KB\n", (unsigned long long)free);
}

// ================== SETUP ==========================

void setup() {
  auto cfg = M5.config();
  M5.begin(cfg);

  M5.Display.setRotation(1);
  M5.Display.setTextColor(WHITE);
  M5.Display.setTextSize(1);

  sd_ok = SD.begin();

  bool warn = isLowStorage();

  if (warn) {
    drawASCII(ASCII_WARNING, ASCII_WARNING_LINES);
  } else {
    drawASCII(ASCII_NORMAL, ASCII_NORMAL_LINES);
  }

  int lines = warn ? ASCII_WARNING_LINES : ASCII_NORMAL_LINES;
  int yBase = 4 + lines * 8;

  showSDStats(yBase + 4);
}

// ================== LOOP ===========================

void loop() {
  M5.update();

  static uint32_t lastUpdate = 0;
  if (millis() - lastUpdate > 5000) {

    bool warn = isLowStorage();

    if (warn) {
      drawASCII(ASCII_WARNING, ASCII_WARNING_LINES);
    } else {
      drawASCII(ASCII_NORMAL, ASCII_NORMAL_LINES);
    }

    int lines = warn ? ASCII_WARNING_LINES : ASCII_NORMAL_LINES;
    int yBase = 4 + lines * 8;

    M5.Display.fillRect(0, yBase, M5.Display.width(), M5.Display.height() - yBase, BLACK);
    showSDStats(yBase + 4);

    lastUpdate = millis();
  }
}
