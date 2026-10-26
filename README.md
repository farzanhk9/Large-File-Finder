from pathlib import Path
from dataclasses import dataclass


@dataclass
class FileInfo:
    path: Path
    size: int


class LargeFileFinder:

    def __init__(self, directory):
        self.directory = Path(directory)
        self.files = []

    def scan(self):

        for item in self.directory.rglob("*"):

            if not item.is_file():
                continue

            try:
                self.files.append(
                    FileInfo(
                        item,
                        item.stat().st_size
                    )
                )

            except PermissionError:
                continue

    @staticmethod
    def format_size(size):

        units = ["B", "KB", "MB", "GB", "TB"]

        for unit in units:

            if size < 1024:
                return f"{size:.2f} {unit}"

            size /= 1024

    def report(self, top=10):

        self.files.sort(
            key=lambda f: f.size,
            reverse=True
        )

        print(f"\nTop {top} Largest Files")
        print("=" * 70)

        for index, file in enumerate(
            self.files[:top],
            start=1
        ):

            print(
                f"{index:2}. "
                f"{self.format_size(file.size):>10}  "
                f"{file.path}"
            )


def main():

    folder = input("Folder path: ").strip()

    finder = LargeFileFinder(folder)

    print("\nScanning...\n")

    finder.scan()

    finder.report()


if __name__ == "__main__":
    main()
