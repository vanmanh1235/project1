#include <iostream>
#include <vector>
#include <string>

using namespace std;

class SanPham {
protected:
    string tenSP;
    string moTa;
    int maSP;
    int soLuong;
    double giaNhap;
    double giaBan;

public:
    SanPham() : maSP(0), soLuong(0), giaNhap(0.0), giaBan(0.0) {}

    virtual void nhapThongTin() {
        cout << "Nhập mã sản phẩm: ";
        cin >> maSP;
        cout << "Nhập tên sản phẩm: ";
        cin.ignore();
        getline(cin, tenSP);
        cout << "Nhập số lượng: ";
        cin >> soLuong;
        cout << "Nhập mô tả: ";
        cin.ignore();
        getline(cin, moTa);
        cout << "Nhập giá nhập: ";
        cin >> giaNhap;
        cout << "Nhập giá bán: ";
        cin >> giaBan;
    }

    virtual double tinhTongTien() const = 0;

    virtual void hienThi() const {
        cout << "Sản Phẩm: " << tenSP << ", Mô Tả: " << moTa << ", Mã: " << maSP
            << ", Số Lượng: " << soLuong << ", Giá Nhập: " << giaNhap << ", Giá Bán: " << giaBan << endl;
    }

    int getMaSP() const {
        return maSP;
    }

    double getGiaBan() const {
        return giaBan;
    }

    int getSoLuong() const {
        return soLuong;
    }
};

class HangCLC : public SanPham {
public:
    double tinhTongTien() const override {
        return getGiaBan() * getSoLuong() * 2;
    }
};

class HangThuong : public SanPham {
public:
    double tinhTongTien() const override {
        return getGiaBan() * getSoLuong() - 100000;
    }
};

class KhachHang {
protected:
    string ten;
    int tuoi;
    int soCMND;

public:
    KhachHang(string ten = "", int tuoi = 0, int soCMND = 0) : ten(ten), tuoi(tuoi), soCMND(soCMND) {}

    virtual void nhapThongTin() {
        cout << "Nhập tên khách hàng: ";
        cin.ignore();
        getline(cin, ten);
        cout << "Nhập tuổi: ";
        cin >> tuoi;
        cout << "Nhập số CMND: ";
        cin >> soCMND;
    }

    virtual void hienThi() const {
        cout << "Khách Hàng: " << ten << ", Tuổi: " << tuoi << ", Số CMND: " << soCMND << endl;
    }

    int getSoCMND() const {
        return soCMND;
    }
};

class KhachThanhVien : public KhachHang {
public:
    // Additional member functions for members
};

class KhachThuong : public KhachHang {
public:
    // Additional member functions for regular customers
};

class HoaDon {
private:
    vector<SanPham*> sanPhams;
    KhachHang* khachHang;
    double tongTien;

public:
    HoaDon(KhachHang* kh) : khachHang(kh), tongTien(0.0) {}

    void themSanPham(SanPham* sp) {
        sanPhams.push_back(sp);
        tongTien += sp->tinhTongTien();
    }

    void hienThiHoaDon() const {
        cout << "Hóa Đơn cho Khách Hàng: " << khachHang->getSoCMND() << endl;
        khachHang->hienThi();
        cout << "Sản phẩm đã mua:\n";
        for (const auto& sp : sanPhams) {
            sp->hienThi();
        }
        cout << "Tổng Tiền Hóa Đơn: " << tongTien << " VND\n";
    }
};

class QuanLySieuThi {
private:
    vector<SanPham*> sanPhams;
    vector<KhachHang*> khachHangs;
    vector<HoaDon*> hoaDons;

public:
    ~QuanLySieuThi() {
        for (auto sp : sanPhams) delete sp;
        for (auto kh : khachHangs) delete kh;
        for (auto hd : hoaDons) delete hd;
    }

    void themSanPham() {
        int choice;
        cout << "Chọn loại sản phẩm (1 - Hàng CLC, 2 - Hàng Thường): ";
        cin >> choice;
        SanPham* sp = nullptr;
        if (choice == 1) {
            sp = new HangCLC();
        }
        else if (choice == 2) {
            sp = new HangThuong();
        }
        sp->nhapThongTin();
        sanPhams.push_back(sp);
    }

    void themKhachHang() {
        int choice;
        cout << "Chọn loại khách hàng (1 - Thành Viên, 2 - Thường): ";
        cin >> choice;
        KhachHang* kh = nullptr;
        if (choice == 1) {
            kh = new KhachThanhVien();
        }
        else if (choice == 2) {
            kh = new KhachThuong();
        }
        kh->nhapThongTin();
        khachHangs.push_back(kh);
    }

    void taoHoaDon() {
        cout << "Nhập số CMND của Khách Hàng: ";
        int soCMND;
        cin >> soCMND;
        KhachHang* kh = nullptr;
        for (auto& k : khachHangs) {
            if (k->getSoCMND() == soCMND) {
                kh = k;
                break;
            }
        }
        if (!kh) {
            cout << "Khách hàng không tồn tại.\n";
            return;
        }

        HoaDon* hd = new HoaDon(kh);
        int maSP;
        do {
            cout << "Nhập mã sản phẩm để thêm vào hóa đơn (0 để kết thúc): ";
            cin >> maSP;
            if (maSP == 0) break;

            SanPham* sp = nullptr;
            for (auto& p : sanPhams) {
                if (p->getMaSP() == maSP) {
                    sp = p;
                    break;
                }
            }
            if (sp) {
                hd->themSanPham(sp);
            }
            else {
                cout << "Sản phẩm không tồn tại.\n";
            }
        } while (maSP != 0);

        hoaDons.push_back(hd);
    }

    void hienThiSanPhams() const {
        for (int i = 0; i < sanPhams.size(); i++) {
            sanPhams[i]->hienThi();
        }
    }



    void hienThiKhachHangs() const {
        for (int i = 0; i < khachHangs.size(); i++) {
            khachHangs[i]->hienThi();
        }
    }


    void hienThiHoaDons() const {
        for (auto& hd : hoaDons) {
            hd->hienThiHoaDon();
        }
    }

    void menu() {
        int choice;
        do {
            cout << "\n### Menu Quản Lý Siêu Thị ###\n";
            cout << "1. Thêm Sản Phẩm\n";
            cout << "2. Thêm Khách Hàng\n";
            cout << "3. Hiển Thị Sản Phẩm\n";
            cout << "4. Hiển Thị Khách Hàng\n";
            cout << "5. Tạo Hóa Đơn\n";
            cout << "6. Hiển Thị Hóa Đơn\n";
            cout << "7. Thoát\n";
            cout << "Nhập lựa chọn của bạn: ";
            cin >> choice;
            switch (choice) {
            case 1:
                themSanPham();
                break;
            case 2:
                themKhachHang();
                break;
            case 3:
                hienThiSanPhams();
                break;
            case 4:
                hienThiKhachHangs();
                break;
            case 5:
                taoHoaDon();
                break;
            case 6:
                hienThiHoaDons();
                break;
            case 7:
                cout << "Đang thoát...\n";
                return;
            default:
                cout << "Lựa chọn không hợp lệ, vui lòng thử lại.\n";
            }
        } while (true);
    }
};

int main() {
    QuanLySieuThi quanLy;
    quanLy.menu();
    return 0;
}
